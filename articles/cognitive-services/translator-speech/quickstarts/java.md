---
title: 'Szybki Start: Java usług Azure Cognitive Services, interfejs API mowy usługi Microsoft Translator | Dokumentacja firmy Microsoft'
description: Pobierz informacje oraz przykłady kodu w celu szybkiego Rozpocznij pracę przy użyciu interfejsu API mowy usługi Microsoft Translator w usługach Microsoft Cognitive Services na platformie Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: 0db55f4064d3415cb647519ea9030936012a8446
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "41988621"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-java"></a>Przewodnik Szybki start dotyczący usługi Microsoft Translator interfejs API rozpoznawania mowy za pomocą języka Java 
<a name="HOLTop"></a>

Ten artykuł pokazuje, jak używać interfejsu API mowy usługi Microsoft Translator do tłumaczenia używany w pliku .wav słów.

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [JDK 7 lub 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) Aby skompilować i uruchomić ten kod. Jeśli masz Ulubione, ale wystarczy edytora tekstów, można użyć Java IDE.

Konieczne będzie następujące pliki.
- [javax.websocket-api-1.1.jar (lub nowszy)](https://mvnrepository.com/artifact/javax.websocket/javax.websocket-api)
- [jetty-http-9.4.11.v20180605.jar (lub nowszy)](https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-http)
- [jetty-we/wy — 9.4.11.v20180605.jar (lub nowszy)](https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-io)
- [jetty-util-9.4.11.v20180605.jar (lub nowszy)](https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-util)
- [websocket-api-9.4.11.v20180605.jar (lub nowszy)](https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-api)
- [9.4.11.v20180605.jar-websocket klient (lub nowszej)](https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-client)
- [websocket — typowe 9.4.11.v20180605.jar (lub nowszej)](https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-common)
- [javax-websocket — klient impl-9.4.11.v20180605.jar (lub nowszy)](https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/javax-websocket-client-impl)
- [klient-serwer jetty-9.4.11.v20180605.jar (lub nowszy)](https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-client)

Konieczne będzie plik wav o nazwie "speak.wav", w tym samym folderze co plik wykonywalny, który kompilujesz z poniższym kodem. Ten plik w formacie .wav należy w module PCM standardowe, 16-bitowych, 16kHz, format mono. Można uzyskać taki plik WAV z [zamiany tekstu na mowę interfejsu API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

Konieczne jest posiadanie [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejs API mowy usługi Microsoft Translator**. Konieczne będzie klucza z płatnej licencji usługi [pulpitu nawigacyjnego platformy Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Tłumaczenie mowy

Poniższy kod wykonuje translację mowy z jednego języka do innego.

1. Utwórz nowy projekt języka Java w Twoim ulubionym środowisku IDE.
2. Dodaj kod, przedstawione poniżej.
3. Zastąp `key` wartością prawidłowy klucz dostępu dla Twojej subskrypcji.
4. Uruchom program.

Config.Java:

```java
import java.util.*;

import javax.websocket.ClientEndpointConfig;

public class Config extends ClientEndpointConfig.Configurator {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace the subscriptionKey string value with your valid subscription key.
    static String key = "ENTER KEY HERE";

    @Override
    public void beforeRequest (Map<String,List<String>> headers) {
        headers.put("Ocp-Apim-Subscription-Key", Arrays.asList (key));
    }
}
```

Client.Java:

```java
import java.io.*;
import java.net.*;
import java.util.*;

import javax.websocket.ClientEndpoint;
import javax.websocket.CloseReason;
import javax.websocket.ContainerProvider;
import javax.websocket.OnClose;
import javax.websocket.OnError;
import javax.websocket.OnMessage;
import javax.websocket.OnOpen;
import javax.websocket.Session;
import javax.websocket.WebSocketContainer;

import org.eclipse.jetty.client.*;
import org.eclipse.jetty.http.*;
import org.eclipse.jetty.io.*;
import org.eclipse.jetty.util.*;
import org.eclipse.jetty.websocket.api.*;
import org.eclipse.jetty.websocket.client.io.*;
import org.eclipse.jetty.websocket.common.scopes.*;

/* Useful reference links:
    https://docs.oracle.com/javaee/7/api/javax/websocket/Session.html
    https://docs.oracle.com/javaee/7/api/javax/websocket/RemoteEndpoint.Basic.html
    https://docs.oracle.com/javaee/7/api/javax/websocket/OnMessage.html
    http://www.oracle.com/technetwork/articles/java/jsr356-1937161.html
*/

@ClientEndpoint(configurator = Config.class)
public class Client {
    static String host = "wss://dev.microsofttranslator.com";
    static String path = "/speech/translate";
    static String params = "?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa";
    static String uri = host + path + params;

    static String input_path = "speak.wav";
    static String output_path = "speak2.wav";

    Session session = null;

    @OnOpen
    public void onOpen(Session session) {
        this.session = session;
        System.out.println ("Connected.");
        SendAudio ();
    }

    @OnMessage
    public void onTextMessage(String message, Session session){
        System.out.println ("Text message received.");
        System.out.println (message);
    }

/*
Use the following signature to receive the message in parts:
    public void onBinaryMessage(byte[] buffer, boolean last, Session session)
Use the following signature to receive the entire message:
    public void onBinaryMessage(byte[] buffer, Session session)
See:
    https://docs.oracle.com/javaee/7/api/javax/websocket/OnMessage.html
*/
    @OnMessage
    public void onBinaryMessage(byte[] buffer, Session session) {
        try {
            System.out.println ("Binary message received.");
            FileOutputStream stream = new FileOutputStream(output_path);
            stream.write(buffer);
            stream.close();
            System.out.println ("Message contents written to file.");
            Close ();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    @OnError
    public void onError(Session session, Throwable e) {
        System.out.println ("Error message received: " + e.getMessage ());
    }

    @OnClose
    public void myOnClose (CloseReason reason) {
        System.out.println ("Close message received: " + reason.getReasonPhrase());
    }

    public void SendAudio() {
        try {
            File file = new File(input_path);
            FileInputStream stream = new FileInputStream (file);
            byte buffer[] = new byte[(int)file.length()];
            stream.read(buffer);
            stream.close();
            sendMessage (buffer);
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void sendMessage(byte[] message) {
        try {
            System.out.println ("Sending audio.");
            OutputStream stream = this.session.getBasicRemote().getSendStream();
            stream.write (message);
/* Make sure the audio file is followed by silence.
 * This lets the service know that the audio file is finished.
 * At 32 bytes per millisecond, this is 10 seconds of silence. */
            System.out.println ("Sending silence.");
            byte silence[] = new byte[320000];
            stream.write (silence);
            stream.flush();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void Connect () throws Exception {
        try {
            System.out.println("Connecting.");
            WebSocketContainer container = ContainerProvider.getWebSocketContainer();
/* The response message might exceed the default max message size of 65536. */
            container.setDefaultMaxBinaryMessageBufferSize(131072);
/* Some code samples show container.connectToServer as returning a Session, but this seems to be false. */
            container.connectToServer(this, new URI(uri));
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void Close () throws Exception {
        try {
            System.out.println("Closing connection.");
            this.session.close ();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }
}
```

Speak.Java:

```java
/*
Download javax.websocket-api-1.1.jar (or newer) from:
    http://central.maven.org/maven2/javax/websocket/javax.websocket-api/1.1/
Download jetty-http-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-http
Download jetty-io-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-io
Download jetty-util-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-util
Download websocket-api-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-api
Download websocket-client-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-client
Download websocket-common-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-common
Download javax-websocket-client-impl-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/javax-websocket-client-impl
Download jetty-client-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-client

Compile and run with:
    javac Config.java -cp .;javax.websocket-api-1.1.jar
    javac Client.java -cp .;javax.websocket-api-1.1.jar;javax-websocket-client-impl-9.4.11.v20180605.jar;websocket-common-9.4.11.v20180605.jar;jetty-util-9.4.11.v20180605.jar;jetty-io-9.4.11.v20180605.jar;websocket-api-9.4.11.v20180605.jar;websocket-client-9.4.11.v20180605.jar;jetty-client-9.4.11.v20180605.jar;jetty-http-9.4.11.v20180605.jar
    javac Speak.java -cp .;javax.websocket-api-1.1.jar;javax-websocket-client-impl-9.4.11.v20180605.jar;websocket-common-9.4.11.v20180605.jar;jetty-util-9.4.11.v20180605.jar;jetty-io-9.4.11.v20180605.jar;websocket-api-9.4.11.v20180605.jar;websocket-client-9.4.11.v20180605.jar;jetty-client-9.4.11.v20180605.jar;jetty-http-9.4.11.v20180605.jar
    java -cp .;javax.websocket-api-1.1.jar;javax-websocket-client-impl-9.4.11.v20180605.jar;websocket-common-9.4.11.v20180605.jar;jetty-util-9.4.11.v20180605.jar;jetty-io-9.4.11.v20180605.jar;websocket-api-9.4.11.v20180605.jar;websocket-client-9.4.11.v20180605.jar;jetty-client-9.4.11.v20180605.jar;jetty-http-9.4.11.v20180605.jar Speak
*/

import java.lang.Thread;

public class Speak {
    public static void main(String[] args) {
        try {
            Client client = new Client ();
            client.Connect ();
            // Wait for the reply.
            Thread.sleep (5000);
            System.out.println ("Press Enter to exit the application at any time.");
            System.in.read();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

**Tłumaczenie mowy odpowiedzi**

Pomyślne wynikiem jest utworzenie pliku o nazwie "speak2.wav". Plik zawiera tłumaczeń wyrazów, używany w "speak.wav".

[Powrót do początku](#HOLTop)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący mowy usługi Translator](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Zobacz także 

[Omówienie funkcji rozpoznawania mowy w usłudze Translator](../overview.md)
[dokumentacja interfejsu API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
