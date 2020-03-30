---
title: Usługi Azure Event Hubs — zdarzenia programu Exchange przy użyciu różnych protokołów
description: W tym artykule pokazano, jak konsumenci i producenci korzystający z różnych protokołów (AMQP, Apache Kafka i HTTPS) mogą wymieniać zdarzenia podczas korzystania z usługi Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: femila
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2019
ms.author: femila
ms.openlocfilehash: 368cc568c40e878338e6b45205e74cba1d0b6378
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372214"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Wymiana zdarzeń między konsumentami i producentami korzystającymi z różnych protokołów: AMQP, Kafka i HTTPS
Usługa Azure Event Hubs obsługuje trzy protokoły dla konsumentów i producentów: AMQP, Kafka i HTTPS. Każdy z tych protokołów ma swój własny sposób reprezentowania wiadomości, więc naturalnie pojawia się następujące pytanie: jeśli aplikacja wysyła zdarzenia do Centrum zdarzeń z jednym protokołem i zużywa je z innym protokołem, co zrobić z różnymi częściami i wartościami zdarzenie wyglądać, gdy przybywają do konsumenta? W tym artykule omówiono najlepsze rozwiązania dla producenta i konsumenta, aby upewnić się, że wartości w ramach zdarzenia są poprawnie interpretowane przez aplikację zużywającą.

Porady zawarte w tym artykule obejmują w szczególności tych klientów, z wymienionych wersji używanych do tworzenia fragmentów kodu:

* Kafka Java (wersja 1.1.1 odhttps://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Klient centrów zdarzeń platformy Microsoft Azure dla języka Java (wersja 1.1.0 odhttps://github.com/Azure/azure-event-hubs-java)
* Klienta centrum zdarzeń platformy Microsoft Azure dla platformy .NET (wersja 2.1.0 odhttps://github.com/Azure/azure-event-hubs-dotnet)
* Usługa Microsoft Azure Service Bus (wersja 5.0.0 odhttps://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (obsługuje tylko producentów)

Inni klienci AMQP mogą zachowywać się nieco inaczej. USŁUGA AMQP ma dobrze zdefiniowany system typów, ale specyfika serializacji typów specyficznych dla języka do i z tego systemu typu zależy od klienta, podobnie jak klient zapewnia dostęp do części komunikatu AMQP.

## <a name="event-body"></a>Treść zdarzenia
Wszystkie klienci usługi Microsoft AMQP reprezentują treść zdarzenia jako niezinterpretowany worek bajtów. Aplikacja tworząca przekazuje sekwencję bajtów do klienta, a aplikacja zużywająca odbiera tę samą sekwencję od klienta. Interpretacja sekwencji bajtów odbywa się w kodzie aplikacji.

Podczas wysyłania zdarzenia za pośrednictwem protokołu HTTPS treść zdarzenia jest zawartością posted, która jest również traktowana jako niezinterpretowane bajty. Łatwo jest osiągnąć ten sam stan u producenta lub konsumenta platformy Kafka za pomocą dostarczonego ByteArraySerializer i ByteArrayDeserializer, jak pokazano w poniższym kodzie:

### <a name="kafka-byte-producer"></a>Kafka byte[] producent

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka bajt[] konsument
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Ten kod tworzy przezroczysty potok bajtów między dwiema połowami aplikacji i umożliwia deweloperowi aplikacji ręczne serializowanie i deserializację w dowolny sposób pożądany, w tym podejmowanie decyzji dotyczących deserializacji w czasie wykonywania, na przykład na podstawie typu lub informacje o nadawcy we właściwościach zestawu użytkownika zdarzenia.

Aplikacje, które mają jeden, stały typ treści zdarzenia może być w stanie użyć innych serializatorów platformy Kafka i deserializatorów do przezroczystego konwertowania danych. Rozważmy na przykład aplikację, która używa JSON. Konstrukcja i interpretacja ciągu JSON odbywa się na poziomie aplikacji. Na poziomie centrum zdarzeń treść zdarzenia jest zawsze ciągiem, sekwencją bajtów reprezentującymi znaki w kodowaniu UTF-8. W takim przypadku producent lub konsument platformy Kafka może skorzystać z dostarczonego stringserializer lub StringDeserializer, jak pokazano w poniższym kodzie:

### <a name="kafka-utf-8-string-producer"></a>Producent sznurków Kafka UTF-8
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka UTF-8 ciąg konsumentów
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

Po stronie AMQP zarówno Java, jak i .NET zapewniają wbudowane sposoby konwertowania ciągów do sekwencji bajtów UTF-8 i z niej. Klienci amqp firmy Microsoft reprezentują zdarzenia jako klasę o nazwie EventData. Poniższe przykłady pokazują, jak serializować ciąg UTF-8 do treści zdarzenia EventData w producencie AMQP i jak deserialize treści zdarzenia EventData do ciągu UTF-8 w konsumenta AMQP.

### <a name="java-amqp-utf-8-string-producer"></a>Java AMQP UTF-8 producent strun
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java AMQP UTF-8 ciąg konsumentów
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>Producent ciągów C# .NET UTF-8
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET UTF-8 konsument ciągu
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Ponieważ kafka jest open-source, deweloper aplikacji można sprawdzić implementację dowolnego serializatora lub deserializer i zaimplementować kod, który produkuje lub zużywa zgodne sekwencji bajtów po stronie AMQP.

## <a name="event-user-properties"></a>Właściwości użytkownika zdarzenia

Właściwości zestawu użytkownika można ustawiać i pobierać zarówno z klientów AMQP (w klientach usługi Microsoft AMQP są nazywane właściwościami), jak i kafka (gdzie są nazywane nagłówkami). Nadawcy https można ustawić właściwości użytkownika na zdarzenie, podając je jako nagłówki HTTP w operacji POST. Jednak kafka traktuje zarówno obiekty zdarzeń, jak i wartości nagłówka zdarzeń jako sekwencje bajtów. Podczas gdy w klientach AMQP wartości właściwości mają typy, które są przekazywane przez kodowanie wartości właściwości zgodnie z systemem typu AMQP.

HTTPS jest szczególnym przypadkiem. W punkcie wysyłania wszystkie wartości właściwości są UTF-8 tekst. Usługa Event Hubs wykonuje ograniczoną ilość interpretacji, aby przekonwertować odpowiednie wartości właściwości na 32-bitowe i 64-bitowe liczby całkowite z podpisem AMQP, 64-bitowe liczby zmiennoprzecinkowe i wartości logiczne. Każda wartość właściwości, która nie pasuje do jednego z tych typów jest traktowana jako ciąg.

Mieszanie tych podejść do wpisywania właściwości oznacza, że konsument platformy Kafka widzi surową sekwencję bajtów zakodowaną w formacie AMQP, w tym informacje o typie usługi AMQP. Natomiast konsument AMQP widzi sekwencję bajtów bez typu wysyłane przez producenta platformy Kafka, które aplikacja musi interpretować.

W przypadku konsumentów platformy Kafka, którzy odbierają właściwości od producentów protokołu AMQP lub HTTPS, należy użyć klasy AmqpDeserializer, która jest wzorowana na innych deserializatorach w ekosystemie platformy Kafka. Interpretuje informacje o typie w sekwencjach bajtów zakodowanych w uodzędnych w ud. W języku AMQP, aby zdesializować bajty danych do typu Java.

Najlepszym rozwiązaniem jest dołączenie właściwości do wiadomości wysyłanych za pośrednictwem protokołu AMQP lub HTTPS. Konsument platformy Kafka można go używać do określenia, czy wartości nagłówka wymagają deserializacji usługi AMQP. Wartość właściwości nie jest ważna. Po prostu potrzebuje dobrze znanej nazwy, którą konsument platformy Kafka może znaleźć na liście nagłówków i odpowiednio dostosować jego zachowanie.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>POLECENIE AMQP do platformy Kafka część 1: tworzenie i wysyłanie zdarzenia w języku C# (.NET) z właściwościami
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP do kafki część 2: użyj AmqpDeserializer do deserializacji tych właściwości w konsumenta platformy Kafka
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Jeśli aplikacja zna oczekiwany typ właściwości, istnieją metody deserializacji, które nie wymagają rzutowania później, ale zgłaszają błąd, jeśli właściwość nie jest oczekiwanego typu.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP do Kafka część 3: inny sposób korzystania z AmqpDeserializer w konsumenta Platformy Kafka
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Idąc w drugą stronę jest bardziej zaangażowany, ponieważ nagłówki ustawione przez producenta platformy Kafka są zawsze postrzegane przez konsumenta usługi AMQP jako nieprzetworzone bajty (typ `System.Byte[]` org.apache.qpid.proton.amqp.Binary dla klienta microsoft azure event hubs dla języka Java lub dla klientów .NET AMQP firmy Microsoft). Najłatwiejszą ścieżką jest użycie jednego z serializatorów dostarczonych przez platformę Kafka do generowania bajtów dla wartości nagłówka po stronie producenta platformy Kafka, a następnie zapisanie zgodnego kodu deserializacji po stronie konsumenta usługi AMQP.

Podobnie jak w amqp-to-Kafka, najlepszym rozwiązaniem, które zaleca się dołączyć właściwość w wiadomościach wysyłanych za pośrednictwem platformy Kafka. Konsument AMQP można użyć właściwości, aby ustalić, czy wartości nagłówka wymagają deserializacji. Wartość właściwości nie jest ważna. Po prostu potrzebuje dobrze znanej nazwy, którą konsument AMQP można znaleźć na liście nagłówków i odpowiednio dostosować jego zachowanie. Jeśli producent platformy Kafka nie można zmienić, jest również możliwe dla aplikacji zużywającej, aby sprawdzić, czy wartość właściwości jest typu binarnego lub bajtowego i próby deserializacji na podstawie typu.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Od kafka do AMQP część 1: tworzenie i wysyłanie zdarzenia z platformy Kafka z właściwościami
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Od platformy Kafka do AMQP część 2: ręcznie deserialize tych właściwości w języku C# (.NET)
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Od kafka do AMQP część 3: ręcznie deserialize tych właściwości w Javie
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>Następne kroki
W tym artykule dowiesz się, jak przesyłać strumieniowo do centrów zdarzeń bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Aby dowiedzieć się więcej o centrach zdarzeń i centrach zdarzeń dla platformy Kafka, zobacz następujące artykuły:  

* [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Dowiedz się więcej na temat usługi Event Hubs dla platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Poznaj więcej przykładów w usłudze Event Hubs dla platformy Kafka](https://github.com/Azure/azure-event-hubs-for-kafka)
* Użyj [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) do [przesyłania strumieniowego zdarzeń z platformy Kafka lokalnie do centrum zdarzeń w chmurze.](event-hubs-kafka-mirror-maker-tutorial.md)
* Dowiedz się, jak przesyłać strumieniowo do Centrów zdarzeń przy użyciu [natywnych aplikacji Platformy Kafka,](event-hubs-quickstart-kafka-enabled-event-hubs.md) [Apache Flink](event-hubs-kafka-flink-tutorial.md)lub [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
