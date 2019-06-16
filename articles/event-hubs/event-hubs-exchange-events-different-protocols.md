---
title: Exchange zdarzeń między aplikacjami, które używają różnych protokołów — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak konsumentów i producentów, które używają różnych protokołów (AMQP, Apache Kafka i HTTPS) programu exchange zdarzenia w przypadku korzystania z usługi Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: e704a2595130a2a815388447ac482ab96789d64a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821767"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Zdarzenia programu Exchange między konsumentów i producentów, które używają różnych protokołów: Protokół AMQP, Kafka i HTTPS
Usługa Azure Event Hubs obsługuje trzy protokoły dla konsumentów i producentów: Protokół AMQP, Kafka i HTTPS. Każda z tych protokołów ma swój własny sposób przedstawiania wiadomości, więc naturalnie pojawia się następujące pytanie: aplikacja wysyła zdarzenia do Centrum zdarzeń za pomocą jednego protokołu i wykorzystuje je z innym protokołem, co zrobić, jeżeli różnych części i wartości Zdarzenie wyglądać po ich nadejściu na użytkownika? W tym artykule omówiono najlepsze rozwiązania dotyczące zarówno producentów i konsumentów upewnić się, że wartości w ramach zdarzenia są prawidłowo interpretowane przez aplikacja odbierająca komunikaty.

Porady w tym artykule omówiono specjalnie tych klientów, za pomocą wymienionych wersji stosowane w opracowywaniu fragmenty kodu:

* Klienta Kafka Java (wersja 1.1.1 z https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Klienta centra zdarzeń Azure firmy Microsoft dla języka Java (w wersji 1.1.0 z https://github.com/Azure/azure-event-hubs-java)
* Klienta centra zdarzeń Azure firmy Microsoft dla platformy .NET (w wersji 2.1.0 z https://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (wersja 5.0.0 z https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (obsługuje wyłącznie producenci)

Inni klienci protokołu AMQP może zachowywać się inaczej. Protokół AMQP charakteryzują się system dobrze zdefiniowanego typu, ale szczegółowe informacje na temat serializacji typów specyficznych dla języka, do i z tego systemu typu jest zależna od klienta, tak jak jak klient zapewnia dostęp do części wiadomości protokołu AMQP.

## <a name="event-body"></a>Treść zdarzenia
Treść zdarzenia wszystkich klientów firmy Microsoft AMQP przedstawić jako niezinterpretowanej zbiór bajtów. Tworzenie aplikacji przekazuje sekwencji bajtów do klienta, a aplikacja odbierająca komunikaty odbiera tej samej kolejności od klienta. Interpretacja sekwencja bajtów ma miejsce w kodzie aplikacji.

Podczas wysyłania zdarzeń za pośrednictwem protokołu HTTPS, treści zdarzenia jest zawartość zaksięgowana, która również jest traktowany jako niezinterpretowanej bajtów. Chodzi o łatwe do osiągnięcia samą stanu w producenta platformy Kafka lub klientów indywidualnych przy użyciu podanego ByteArraySerializer i ByteArrayDeserializer, jak pokazano w poniższym kodzie:

### <a name="kafka-byte-producer"></a>Platforma Kafka byte [] producenta

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Platforma Kafka byte [] konsumenta
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Ten kod tworzy potok przezroczyste bajtów między dwiema połowami aplikacji i umożliwia deweloperom aplikacji ręcznie serializacji i deserializacji w jakikolwiek sposób potrzeby podejmowania decyzji dotyczących deserializacji w czasie wykonywania, w tym na przykład na podstawie typu lub informacje o nadawcy we właściwościach zestawu użytkownika o zdarzeniu.

Aplikacje, które mają jeden, typ treści zdarzeń w stałym można używać innych serializatory platformy Kafka i deserializers do sposób przezroczysty konwertują dane. Rozważmy na przykład aplikację, która używa formatu JSON. Konstrukcja i interpretację ciągu JSON odbywa się na poziomie aplikacji. Na poziomie usługi Event Hubs treści zdarzenia to zawsze ciąg sekwencję bajtów reprezentujących znaków kodowania UTF-8. W tym przypadku producenta platformy Kafka lub klientów indywidualnych korzystać z zalet podany StringSerializer lub StringDeserializer jak pokazano w poniższym kodzie:

### <a name="kafka-utf-8-string-producer"></a>Producent ciągu Kafka UTF-8
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Konsument ciągu Kafka UTF-8
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

Na stronie protokołu AMQP Java i .NET umożliwiają wbudowanych do konwersji ciągów znaków do i z sekwencji bajtów UTF-8. Klienci firmy Microsoft AMQP reprezentują zdarzenia jako klasę o nazwie EventData. W poniższych przykładach pokazano sposób serializacji ciąg UTF-8 w treści zdarzenia EventData w jeden z producentów AMQP oraz deserializuje treść zdarzenia EventData na ciąg UTF-8 w konsumenta protokołu AMQP.

### <a name="java-amqp-utf-8-string-producer"></a>Producent ciąg języka Java AMQP UTF-8
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Konsument ciąg języka Java AMQP UTF-8
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C# .NET UTF-8 string producer
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C#Konsument ciągu .NET UTF-8
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Platforma Kafka jest typu open source, Deweloper aplikacji można sprawdzić wykonania dowolnego elementu serializującego lub deserializer i zaimplementować kod, który produkuje lub zużywa zgodne sekwencji bajtów stronie protokołu AMQP.

## <a name="event-user-properties"></a>Właściwości użytkownika zdarzenia

Ustawianie użytkownika właściwości można ustawić i pobierane z obaj klienci protokołu AMQP (w klientach Microsoft AMQP nazywane są właściwości) i Kafka (gdy są one nazywane nagłówków). Nadawców HTTPS można ustawić właściwości użytkownika na zdarzenie, podając jako nagłówków HTTP w ramach operacji POST. Jednak Kafka traktuje treści zdarzenia i wartości nagłówka zdarzenia jako sekwencje bajtów. Dla klientów protokołu AMQP z wartości właściwości mają typy, które są przekazywane przez wartości właściwości, zgodnie z protokołu AMQP system typu kodowania.

Protokół HTTPS jest przypadkiem szczególnym. W momencie wysyłania, wszystkie wartości właściwości są wartościami tekstowymi UTF-8. Usługa Event Hubs ma ograniczoną ilość interpretacji można przekonwertować wartości odpowiednich właściwości zakodowane w formacie protokołu AMQP 32-bitowych i 64-bitowe liczby całkowite ze znakiem, 64-bitowych liczb zmiennoprzecinkowych i wartości logiczne. Dowolnej wartości właściwości, która nie pasuje do jednej z tych typów jest traktowany jako ciąg.

Mieszanie te podejścia do pisania właściwości oznacza, że odbiorcy platformy Kafka widzi sekwencji bajtowych zakodowane w formacie protokołu AMQP, w tym informacje o typie protokołu AMQP. Natomiast konsumenta AMQP widzi sekwencji bajtów danych wysyłanych przez producenta platformy Kafka, który należy interpretować aplikacji.

Odbiorcy platformy Kafka, które odbierają właściwości od producentów protokołu AMQP lub HTTPS można użyć w klasie AmqpDeserializer jest modelowane innych deserializers należący do ekosystemu platformy Kafka. Interpretuje informacji o typie w sekwencji bajtów zakodowane w formacie protokołu AMQP do deserializacji bajtów danych do typu języka Java.

Najlepszym rozwiązaniem firma Microsoft zaleca, zawierają właściwość w wiadomościach przesyłanych za pośrednictwem protokołu AMQP lub HTTPS. Odbiorcy platformy Kafka służy do określenia, czy wartości nagłówka deserializacji protokołu AMQP. Wartość właściwości nie jest ważna. Po prostu potrzeba dobrze znaną nazwą, że odbiorcy platformy Kafka, można znaleźć w lista nagłówków i odpowiednio dostosować jego zachowanie.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>Protokół AMQP do platformy Kafka, część 1: tworzenie i wysyłanie zdarzeń C# (.NET) za pomocą właściwości
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>Protokół AMQP do platformy Kafka, część 2: Użyj AmqpDeserializer deserializować tych właściwości w odbiorcy platformy Kafka
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

Jeśli aplikacja zna oczekiwany typ właściwości, brak metody deserializacji, które nie wymagają rzutowania później, ale sygnalizować błąd, jeśli właściwość nie jest oczekiwanego typu.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>Protokół AMQP do platformy Kafka, część 3: inny sposób korzystania z AmqpDeserializer w odbiorcy platformy Kafka
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

Przechodząc w kierunku przeciwnym bardziej uczestniczy, ponieważ nagłówki ustawione przez producenta platformy Kafka zawsze są widoczne dla klientów protokołu AMQP jako bajtów raw (wpisz org.apache.qpid.proton.amqp.Binary dla klienta Microsoft Azure Event Hubs dla języka Java, lub `System.Byte[]` dla firmy Microsoft .NET Protokół AMQP klientów). Najłatwiejszą drogą jest skorzystanie z serializatory dostarczone przez platformę Kafka do generowania bajtów dla wartości nagłówka strony producenta platformy Kafka i następnie napisać kod deserializacji zgodne po stronie klienta protokołu AMQP.

Podobnie jak w przypadku protokołu AMQP do Kafka, najlepszym rozwiązaniem jest zalecane jest umieszczenie właściwość w komunikaty wysyłane za pośrednictwem platformy Kafka. Klienta protokołu AMQP, można użyć właściwości do określenia, czy wartości nagłówka deserializacji. Wartość właściwości nie jest ważna. Po prostu potrzeba dobrze znaną nazwą, że odbiorcy AMQP można znaleźć w lista nagłówków i odpowiednio dostosować jego zachowanie. Jeśli nie można zmienić producenta platformy Kafka, jest również możliwe, że aplikacja odbierająca komunikaty sprawdzić, czy wartość właściwości jest typu binary lub bajt i podjąć próbę deserializacji, w zależności od typu.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka AMQP Part 1: tworzenie i wysyłanie zdarzeń z usługi Kafka z właściwościami
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

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Platformy Kafka na protokół AMQP część 2: ręcznie wykonać deserializacji tych właściwości w C# (.NET)
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

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Platformy Kafka na protokół AMQP część 3: ręcznie wykonać deserializacji tych właściwości w języku Java
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

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób przesyłania strumieniowego usługi Event Hubs z obsługą platformy Kafka bez konieczności zmiany klientów protokołu lub uruchamiania własnych klastrów. Aby dowiedzieć się więcej na temat usługi Event Hubs i usługi Event Hubs dla platformy Kafka, zobacz następujące artykuły:  

* [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Dowiedz się więcej na temat usługi Event Hubs dla platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Poznaj więcej przykładów w usłudze Event Hubs dla platformy Kafka](https://github.com/Azure/azure-event-hubs-for-kafka)
* Użyj [narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) do [Przesyłaj strumieniowo wydarzenia z platformy Kafka w środowisku lokalnym, do platformy Kafka włączone usługi Event Hubs w chmurze.](event-hubs-kafka-mirror-maker-tutorial.md)
* Dowiedz się, jak przesyłać strumieniowo do platformy Kafka włączone za pomocą usługi Event Hubs [natywnych aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md), lub [Akka strumieni](event-hubs-kafka-akka-streams-tutorial.md)
