---
title: How does SSL works
date: 2017-10-19 17:28:00 Z
tags: SSL CSR Keytool
layout: post
thumbnail: https://douglasjd.github.io/blog/images/ssl.jpg 100x100
---

Guide
============
- SSL related knowledge
- Create a keystore and generate the key pair.
- Export self-signed certification from keystore.
- Create a new truststore
- Generate a CSR and Request a Signed Certificate from a CA (Optional)
- Enable SSLv3 (Optional)
- Source Code (SSL Socket Server)
- Source Code (SSL Socket Client)

Create a keystore and generate the key pair.
============
The first thing you need to do is create a keystore and generate the key pair. You could use a command such as the following:
```
$  keytool -genkeypair -dname "cn=Douglas JD, ou=JavaSoft, o=Oracle, c=SG" -alias svrkeys -keyalg RSA -keypass pass123 -keystore svr_keystore.jks -storepass pass321 -validity 180
```

Here the command was named -genKey in earlier release.  While the old one is till supproted, -genkeypair is preferred going forward.


Export self-signed certification from keystore.
==========

```
$ keytool -export -keystore svr_keystore.jks -alias svrkeys -file svr_selfsigned.cer
Enter keystore password:  pass321
```

Print the self-signed cert as following,
```
$ keytool -printcert -file svr_selfsigned.cer
Owner: CN=Douglas JD, OU=JavaSoft, O=Oracle, C=SG
Issuer: CN=Douglas JD, OU=JavaSoft, O=Oracle, C=SG
Serial number: 450824f5
Valid from: Fri Oct 20 10:43:38 SGT 2017 until: Wed Apr 18 10:43:38 SGT 2018
Certificate fingerprints:
         MD5:  16:ED:F9:1F:A4:8C:35:5C:F5:8D:53:86:3A:41:A0:AD
         SHA1: 4C:E3:0A:44:1C:93:8F:37:17:E7:F6:F1:42:64:F4:29:67:F1:C5:83
         SHA256: C6:F9:83:90:C1:D5:5C:59:79:22:2D:11:AC:46:17:45:EE:A9:98:2B:D3:99:8E:88:0D:4F:47:26:BA:0C:58:54
         Signature algorithm name: SHA256withRSA
         Version: 3

Extensions:

#1: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 77 12 73 80 19 76 58 55   A8 61 26 DD BD CC 38 1C  w.s..vXU.a&...8.
0010: 14 E4 9C 8C              
```

Create a new truststore and import the cert.
===================
```
$ keytool -import -file svr_selfsigned.cer -alias svrkeys -keystore svc_truststore.jks
Enter keystore password:  pass4321
Re-enter new password: pass4321
Owner: CN=Douglas JD, OU=JavaSoft, O=Oracle, C=SG
Issuer: CN=Douglas JD, OU=JavaSoft, O=Oracle, C=SG
Serial number: 450824f5
Valid from: Fri Oct 20 10:43:38 SGT 2017 until: Wed Apr 18 10:43:38 SGT 2018
Certificate fingerprints:
         MD5:  16:ED:F9:1F:A4:8C:35:5C:F5:8D:53:86:3A:41:A0:AD
         SHA1: 4C:E3:0A:44:1C:93:8F:37:17:E7:F6:F1:42:64:F4:29:67:F1:C5:83
         SHA256: C6:F9:83:90:C1:D5:5C:59:79:22:2D:11:AC:46:17:45:EE:A9:98:2B:D3:99:8E:88:0D:4F:47:26:BA:0C:58:54
         Signature algorithm name: SHA256withRSA
         Version: 3

Extensions:

#1: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 77 12 73 80 19 76 58 55   A8 61 26 DD BD CC 38 1C  w.s..vXU.a&...8.
0010: 14 E4 9C 8C                                        ....
]
]

Trust this certificate? [no]:  yes
Certificate was added to keystore
```

Enable SSLv3 (Optional)
===================

Empty the disable algorithm list
```
Security.setProperty("jdk.tls.disabledAlgorithms", "");
```

Retrieve the SSLv3 context
```
final SSLContext sslContext = SSLContext.getInstance(PROTOCOL);
```


Generate a CSR and Request a Signed Certificate from a CA (Optional)
==========================
If you want the cert be signed from a CA, you need to create a CSR(Certificate Signing Request) file and submit to a CA.
I am not going to go detail on this section as we use self-signed cert.


Souce Code (SSL Server Socket)
====================
```

package sample.security.socket;

import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.net.ServerSocket;
import java.security.KeyStore;
import java.security.Security;

import javax.net.ssl.KeyManagerFactory;
import javax.net.ssl.SSLContext;
import javax.net.ssl.SSLServerSocketFactory;
import javax.net.ssl.SSLSocket;
import javax.net.ssl.TrustManagerFactory;

import lombok.extern.slf4j.Slf4j;
import sample.security.socket.util.SecurityUtils;

@Slf4j
public class SSLServerSocket {

	static {

		// You can set below setting in gradle as below.
		// applicationDefaultJvmArgs = [
		// "-Djavax.net.ssl.keyStore=./keystore.jks",
		// "-Djavax.net.ssl.keyStorePassword=Password1",
		// ]
		// SSLv3 is included in default disabled algorithms. It resets disabled
		// algorithms and supports SSLv3.
		Security.setProperty("jdk.tls.disabledAlgorithms", "");

		System.setProperty("javax.net.ssl.keyStore", "svr_keystore.jks");
		System.setProperty("javax.net.ssl.storePassword", "pass321");
		System.setProperty("javax.net.ssl.keyPassword", "pass123");

		System.setProperty("javax.net.ssl.trustStore", "svr_truststore.jks");
		System.setProperty("javax.net.ssl.trustStorePassword", "pass4321");

		System.setProperty("socket.security.protocol", "SSLv3");
		System.setProperty("socket.security.port", "8087");
		System.setProperty("socket.security.buffer.size", "2048");

	}

	static final int BUFFER_SIZE = Integer.parseInt(System.getProperty("socket.security.buffer.size", "2048"));
	static final int PORT = Integer.parseInt(System.getProperty("socket.security.port", "8070"));
	static final String PROTOCOL = System.getProperty("socket.security.protocol", "SSLv3");

	static final String KEYSTORE_FILE = System.getProperty("javax.net.ssl.keyStore"),
			STORE_PASSWORD = System.getProperty("javax.net.ssl.storePassword"),
			KEY_PASSWORD = System.getProperty("javax.net.ssl.keyPassword"),

			TRUSTSTORE_FILE = System.getProperty("javax.net.ssl.trustStore"),
			TRUSTSTORE_PASSWORD = System.getProperty("javax.net.ssl.trustStorePassword");

	public static void main(String[] args) throws Exception {
		log.info("Starting server...");
		log.debug("Port: {}, Protocol: {}", PORT, PROTOCOL);

		log.debug("Keystore File: {}", KEYSTORE_FILE);
		KeyManagerFactory kmf = null;
		if (KEYSTORE_FILE != null && STORE_PASSWORD != null) {
			final KeyStore keyStore = SecurityUtils.loadFromFile(KEYSTORE_FILE, STORE_PASSWORD);
			log.debug("Key Store loaded");
			kmf = KeyManagerFactory.getInstance(KeyManagerFactory.getDefaultAlgorithm());
			kmf.init(keyStore, KEY_PASSWORD.toCharArray());
		}

		log.debug("Truststore File: {}", TRUSTSTORE_FILE);
		TrustManagerFactory tmf = null;
		if (TRUSTSTORE_FILE != null && TRUSTSTORE_PASSWORD != null) {
			final KeyStore trustStore = SecurityUtils.loadFromFile(TRUSTSTORE_FILE, TRUSTSTORE_PASSWORD);
			log.debug("Trust Store loaded");
			tmf = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
			tmf.init(trustStore);
		}
		final SSLContext sslContext = SSLContext.getInstance(PROTOCOL);
		sslContext.init(kmf.getKeyManagers(), tmf.getTrustManagers(), null);
		log.debug("SSL Context initialized");


		try {
			final SSLServerSocketFactory sslServerSocketfactory = sslContext.getServerSocketFactory();
			ServerSocket sslServerSocket = sslServerSocketfactory.createServerSocket(PORT);
			log.debug("Server socket created");
			log.info("Server started and listening for connection @ {} for {}", PORT, PROTOCOL);

			BufferedInputStream is = null;
			BufferedOutputStream os = null;
			while (true) {
				final SSLSocket sslSocket = (SSLSocket) sslServerSocket.accept();
				log.info("Accept connection from client {}", sslSocket.getInetAddress());
				is = new BufferedInputStream(sslSocket.getInputStream());
				os = new BufferedOutputStream(sslSocket.getOutputStream());
				//
				final byte[] buffer = new byte[BUFFER_SIZE];
				String clientMessage = "";
				for (int len = is.read(buffer); len >= 0; len = is.read(buffer)) {
					clientMessage += new String(buffer, 0, len);
					if (clientMessage.endsWith("\n")) {
						break;
					}
				}
				log.debug("Received SSL Client Message:\r\n{}",clientMessage);    
				os.write("Hello Client\n".getBytes());
				os.flush();
			}

		} finally {
			log.info("Shutdown server started...");
		}
	}

}
```


Source Code (SSL Socket Client)
====================
```

@Slf4j
public class SSLClientSocket {



	public static void main(String[] args) throws Exception {
		log.info("Starting SSL Socket Client...");

		//Skip the code to initilize sslContext. Please refer to SSLServerSocket

		SSLSocketFactory socketFactory = sslContext.getSocketFactory();

		SSLSocket client = (SSLSocket) socketFactory.createSocket(SERVER_IP, PORT);

		/*
		 * send http request
		 *
		 * Before any application data is sent or received, the SSL socket will do SSL
		 * handshaking first to set up the security attributes.
		 *
		 * SSL handshaking can be initiated by either flushing data down the pipe, or by
		 * starting the handshaking by hand.
		 *
		 * Handshaking is started manually in this example because PrintWriter catches
		 * all IOExceptions (including SSLExceptions), sets an internal error flag, and
		 * then returns without throwing the exception.
		 *
		 * Unfortunately, this means any error messages are lost, which caused lots of
		 * confusion for others using this code. The only way to tell there was an error
		 * is to call PrintWriter.checkError().
		 */
		client.startHandshake();

		BufferedInputStream is = new BufferedInputStream(client.getInputStream());
		BufferedOutputStream os = new BufferedOutputStream(client.getOutputStream());

		os.write("Hellp SSL Server!\n".getBytes());
		os.flush();

		final byte[] buffer = new byte[BUFFER_SIZE];
		String serverMessage = "";
		for (int len = is.read(buffer); len >= 0; len = is.read(buffer)) {
			serverMessage += new String(buffer, 0, len);
			if (serverMessage.endsWith("\n")) {
				break;
			}
		}
		log.debug("Received SSL Client Message:\r\n{}", serverMessage);

	}

}

```
**Note,
The example is tested in same server.  
If you want to run in two servers, you need to create two pairs of keystore and trust store.
1. svr_keystore.  
2. svr_truststore. (Import the cert from SSLClientSocket Server)
3. cli_keystore.
4. cli_truststore. (Import the cert from SSLServerSocket server)
