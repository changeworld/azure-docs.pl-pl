---
title: 'Analytics Platform: porównanie systemu Apache Storm Stream Analytics | Dokumentacja firmy Microsoft'
description: Uzyskaj wskazówki dotyczące wybierania platforma do analiz chmury za pomocą porównania Apache Storm Stream Analytics. Zrozumieć funkcje i różnice.
keywords: Platforma do analiz, analytics platform, platforma do analiz chmury, porównanie storm
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: b9aac017-9866-4d0a-b98f-6f03881e9339
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/27/2017
ms.author: jeanb
ms.openlocfilehash: 48599c362c5db50ebec3acd0aaa4359439edb00b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="choosing-a-streaming-analytics-platform-comparing-apache-storm-and-azure-stream-analytics"></a>Wybieranie przesyłania strumieniowego platforma do analiz: porównanie systemu Apache Storm i usługi Azure Stream Analytics
Platforma Azure oferuje wiele rozwiązań do analizowania danych przesyłania strumieniowego: [analiza strumienia Azure](https://docs.microsoft.com/azure/stream-analytics/) i [Apache Storm w usłudze Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-storm/). Obu platform analytics zapewnia korzyści rozwiązania typu PaaS. Ale platformy niektóre istotne różnice w ich możliwości, jak również w sposób konfigurowania i zarządzania nimi. 

W tym artykule przedstawiono porównanie side-by-side funkcji, aby ułatwić wybór między Apache Storm i usługą Azure Stream Analytics jako platforma do analiz chmury. 

## <a name="general-features"></a>Funkcje ogólne

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm w usłudze HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Typu open source?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Nie. Usługa Azure Stream Analytics jest zastrzeżonych Microsoft oferty.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Tak. Apache Storm to technologia Apache licencji.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Pomoc techniczna firmy Microsoft?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Yes </p>
            </td>
            <td width="246" valign="top">
                <p>
Yes </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Wymagania sprzętowe</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Brak. Usługa Azure Stream Analytics jest usługą platformy Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Brak. Apache Storm jest usługą platformy Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Jednostki najwyższego poziomu</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Użytkownicy, wdrażanie i monitorowanie zadań przesyłania strumieniowego.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Użytkownicy, wdrażanie i monitorowanie całego klastra, który może obsługiwać wiele zadań Storm, a także innych obciążeń (w tym partii).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Cennik</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Cena za ilości danych przetwarzanych i liczbę jednostek przesyłania strumieniowego, wymagane na godzinę, że zadanie zostało uruchomione. 
                </p>
                    <p>Aby uzyskać więcej informacji, zobacz <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">cennik usługi analiza strumienia</a>.</p>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Jednostka zakupu jest oparty na klastrze, a jest rozliczana na podstawie czasu, który klaster działa, niezależnie od zadania wdrożone.
                </p>
                <p>
Aby uzyskać więcej informacji, zobacz <a href="http://azure.microsoft.com/pricing/details/hdinsight/">cennik usługi HDInsight</a>.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring"></a>Tworzenie

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm w usłudze HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Możliwości: DSL SQL?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Tak. Stream Analytics zapewnia języka przypominającego SQL do tworzenia przekształcenia.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Nie. Użytkownicy pisania kodu w języku Java lub C# lub przy użyciu interfejsów API języka Trident.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Możliwości: Operatory czasowe?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Agregacjami i sprzężenia danych czasowych są obsługiwane domyślnie.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Operatory czasowe musi być implementowana przez użytkownika.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Środowisko programistyczne</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Użytkownicy mogą tworzenia, debugowania i monitorowania zadań za pośrednictwem portalu Azure, przy użyciu przykładowych danych pochodzących z strumień na żywo.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Użytkownicy przy użyciu platformy .NET można rozwijać, debugowania i monitorować za pomocą programu Visual Studio. Za pomocą języka Java lub innych języków umożliwia IDE wybranych przez nich.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Obsługa debugowania</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Podstawowe zadania stanu oraz operacji dzienniki są dostępne do debugowania. Analiza strumienia obecnie nie zezwala użytkownikom na określanie zawartości lub ilości zawartości znajduje się w dziennikach (tj. tryb informacji pełnej).
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Szczegółowe dzienniki są dostępne. Użytkownicy mogą uzyskiwać dostęp do dzienników w Visual Studio lub logowanie do klastra i bezpośrednie uzyskiwanie dostępu do dzienników.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Rozszerzalność przy użyciu kodu niestandardowego?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Częściowo obsługiwać funkcje UDF języka JavaScript. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-javascript-user-defined-functions">integracji UDF języka JavaScript</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Tak. Użytkownicy mogą pisanie kodu niestandardowego w C#, Java lub dowolnego innego języka, obsługiwane na Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-inputs-and-outputs"></a>Źródła danych (dane wejściowe) i dane wyjściowe ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm w usłudze HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Źródła danych wejściowych</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Magazyn Azure Event Hubs i obiektów Blob platformy Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Łączniki są dostępne dla usługi Azure Event Hubs, usługi Azure Service Bus, Kafka i inne. Użytkownicy mogą tworzyć dodatkowe łączniki przy użyciu kodu niestandardowego.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formaty danych wejściowych</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Avro, JSON, CSV </p>
            </td>
            <td width="246" valign="top">
                <p>
Użytkownicy mogą zaimplementować dowolnym formacie przy użyciu kodu niestandardowego.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>dane wyjściowe</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Zadanie przesyłania strumieniowego może mieć wielu wyjść. Obsługiwane dane wyjściowe są usługi Azure Event Hubs, obiektów Blob platformy Azure magazynu tabel Azure magazynu, bazy danych SQL Azure i usługi Power BI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
STORM obsługuje wiele dane wyjściowe w topologii, a każdy dane wyjściowe mogą mieć niestandardową logikę na potrzeby przetwarzania podrzędnego. STORM zawiera łączniki dla usługi Power BI, Azure Event Hubs, obiektów Blob platformy Azure magazynu, bazy danych Azure rozwiązania Cosmos, SQL i bazy danych HBase. Użytkownicy mogą tworzyć dodatkowe łączniki przy użyciu kodu niestandardowego.    
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formaty kodowania danych</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Dane muszą być sformatowane w formacie UTF-8.
                </p>
            </td>   
            <td width="246" valign="top">
                <p>
Użytkownicy mogą zaimplementować żadnych format kodowania danych przy użyciu kodu niestandardowego.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="management-and-operations"></a>Zarządzanie i operacje ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm w usłudze HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Model wdrażania zadania</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Portalu Azure, programu PowerShell i interfejsów API REST.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Portalu Azure, programu PowerShell programu Visual Studio i interfejsów API REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Monitorowanie (Statystyka, liczniki itp.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Monitorowanie jest implementowane przy użyciu portalu Azure i interfejsów API REST. Użytkownicy, można również skonfigurować alerty platformy Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Monitorowanie jest zaimplementowany przy użyciu interfejsu użytkownika platformy Storm i interfejsów API REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Skalowalność</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Skalowalność jest określana przez liczbę jednostek przesyłania strumieniowego (SUs) dla każdego zadania. Każda jednostka przesyłania strumieniowego przetwarza maksymalnie 1 MB na sekundę, z maksymalną liczbę jednostek 50. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-jobs">skali w celu zwiększenia przepływności</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Skalowalność zależy od liczby węzłów w klastrze HDInsight Storm. Górny limit liczby węzłów jest zdefiniowana przez użytkownika przydziału platformy Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Limity przetwarzania danych</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Użytkownicy mogą zwiększyć przetwarzania danych lub optymalizować koszty, zwiększ lub Zmniejsz liczbę jednostek przesyłania strumieniowego, z górnym limitem 1 GB na sekundę.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Użytkownicy mogą być skalowane rozmiar klastra w górę lub w dół.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Zatrzymaj/wznowień</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Zatrzymaj i wznowić z ostatniego miejsca zatrzymana.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Zatrzymaj i wznowić z ostatniego umieść zatrzymania oparte na znak wodny.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Aktualizacja usługi i framework</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Automatyczne stosowanie poprawek bez przestojów.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Automatyczne stosowanie poprawek bez przestojów.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Ciągłość prowadzenia działalności biznesowej poprzez wysokiej dostępności usługi za pomocą gwarantowane umowy SLA</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <ul>
                <li>Umowa SLA z wartością 99,9% czasu pracy</li>
                <li>Automatycznego odzyskiwania z błędami</li>
                <li>Wbudowane odzyskiwanie stanowe operatory czasowe</li>
                </ul>
            </td>
            <td width="246" valign="top">
                <p>
SLA 99,9% czasu pracy klaster Storm. 
                </p>
                <p>
Apache Storm to odpornej na uszkodzenia platformy przesyłania strumieniowego. Jednak jest odpowiedzialny za użytkownika, aby upewnić się, że zadania przesyłania strumieniowego działać bez przerywania.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="advanced-features"></a>Funkcje zaawansowane ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm w usłudze HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Obsługa zdarzeń późne przyjęcia i poza kolejnością.</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Wbudowane zasady można skonfigurować można zmienić kolejność zdarzeń, upuszczania lub Dostosuj czas trwania zdarzenia.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Użytkownicy muszą implementować logiki do obsługi tego scenariusza.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Dane referencyjne</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Dane referencyjne jest dostępna z magazynu obiektów Blob platformy Azure z maksymalnie 100 MB pamięci podręcznej. Dane referencyjne są odświeżane przez usługę.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Brak ograniczeń na rozmiar danych. Łączniki są dostępne dla bazy danych HBase, bazy danych rozwiązania Cosmos platformy Azure, programu SQL Server i Azure. Użytkownicy mogą tworzyć dodatkowe łączniki przy użyciu kodu niestandardowego. Dane referencyjne musi zostać odświeżona przy użyciu kodu niestandardowego.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integracja z uczenia maszynowego</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Opublikowane podczas tworzenia zadania można skonfigurować jako funkcje modeli uczenia maszynowego Azure. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-with-machine-learning-functions">skali dla funkcji Machine Learning</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Dostępne za pośrednictwem Storm elementów Bolt.
                </p>
            </td>
        </tr>
    </tbody>
</table>
