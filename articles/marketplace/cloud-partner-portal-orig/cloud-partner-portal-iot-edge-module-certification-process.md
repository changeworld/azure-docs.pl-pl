---
title: Certyfikacja moduł usługi IoT Edge | Dokumentacja firmy Microsoft
description: Zatwierdź moduł usługi IoT Edge w portalu Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c11995a3db1987de7969ee476a1c69a1025be5fd
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810269"
---
# <a name="the-iot-edge-module-certification-process"></a>Proces certyfikacji moduł usługi IoT Edge

W tym artykule opisano kroki i wymagania, aby przeprowadzić certyfikację moduł usługi IoT Edge do publikowania w portalu Azure Marketplace. 

>[!Note]
>Jest to tymczasowy dokument. Portal Marketplace z modułu IoT Edge jest tworzona w sposób równoległy, a w tym artykule zostanie zastąpiona przez publiczną dokumentację.

## <a name="understanding-an-iot-edge-module"></a>Opis moduł usługi IoT Edge

Terminologia modułu:

-   A **obraz modułu** to pakiet zawierający wersję oprogramowania, który definiuje moduł.

-   A **wystąpienia modułu** jest konkretnej jednostki obliczeń systemem obraz modułu urządzenia usługi IoT Edge. Wystąpienia modułu jest uruchomiona przez środowisko uruchomieniowe usługi IoT Edge.

Moduły mogą również obejmować moduł IoT, zestaw SDK, który korzysta z następującą terminologią:

-   A **tożsamości modułu** to fragment informacji (w tym poświadczenia zabezpieczeń), przechowywane w usłudze IoT Hub, skojarzonej z każdym wystąpieniem modułu.

-   A **bliźniaczą reprezentację modułu** to dokument JSON przechowywany w usłudze IoT Hub, który zawiera informacje o stanie dla wystąpienia modułu, w tym metadane, konfiguracje i warunki.

-   **Zestawy SDK** służących do tworzenia niestandardowych modułów w wielu językach, takich jak: C\#, C, Python, Java i Node.JS.

## <a name="the-onboarding-process-for-an-iot-edge-module"></a>Proces dołączania moduł usługi IoT Edge

Poniższy zrzut ekranu przedstawia proces moduł usługi IoT Edge, staje się publiczny w portalu Azure Marketplace.

![Proces certyfikacji](./media/cloud-partner-portal-iot-edge-module-certification-process/onboarding-process.png)

### <a name="onboarding-step-details"></a>Szczegóły kroku dołączania

-   **Krok 1** — partnerzy Prześlij swoje oferty z typu oferty **moduł usługi IoT Edge** w narzędziu portalu Cloud Partner przez zespół witryny Azure Marketplace. Musi być oficjalne partnerem MS, aby uzyskać dostęp do portalu Cloud Partner narzędzia. Aby uzyskać więcej informacji, zobacz [przewodnik wydawcy](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

-   **Krok 2** — Marketplace automatycznie uruchamia oprogramowanie antywirusowe i sprawdza chroniące przed złośliwym kodem. Zespół IoT można uruchomić niestandardowego pozyskiwania zautomatyzowane testy na tym etapie.

-   **Krok 3** — moduł jest publiczny. Ta opcja jest wyświetlana w portalu marketplace i kontenerze mogą być anonimowo ściągane z adresu URL. Na przykład *marketplace.azurecr.io/module-identifier*.

## <a name="iot-edge-module-certification-criteria"></a>Kryteria certyfikacji moduł usługi IoT Edge

Poniżej przedstawiono główne wymagania, które muszą zostać spełnione dla modułu usługi IoT Edge certyfikowane i opublikowania w portalu Azure Marketplace.

>[!Note]
>Te wymagania mogą ulec zmianie. Będziesz otrzymywać powiadomienia z wyprzedzeniem i być podany czas na aktualizację kontenerów, dzięki czemu mogą oni zaktualizowany wymagania.

### <a name="technical-requirements"></a>Wymagania techniczne

**Można moduł usługi IoT Edge**

-   Tylko zgodnego z platformą docker kontenery są obecnie obsługiwane jako moduły usługi IoT Edge. Moduł musi być uruchomiona na [Moby](https://mobyproject.org/). 

    >[!Note]
    >Kontenery platformy docker prawdopodobnie będzie działać z Moby, ponieważ Moby jest podstawowy projekt typu open source dla platformy Docker.

-   Partner musi zapewnić następującymi ustawieniami domyślnymi: 

    -   Domyślnie **tag** (który nie może być pusta.

    -   Domyślnie **CreateOptions, można żądań** (która może być pusta.)

    -   Jeśli przy użyciu modułu zestawu SDK usługi IoT, domyślny **bliźniaczej reprezentacji** (która może być pusta.)

    -   Jeśli przy użyciu modułu zestawu SDK usługi IoT, domyślny **trasy** (która może być pusta.)

**Obsługa różnych platform**

-   Tylko w przypadku platform, które są **jest ogólnie dostępna** w IoT Edge w warstwie 1 (zarejestrowane w [pomocy technicznej usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)) musi być obsługiwana. Na przykład oznacza to, obecnie obsługi następujących systemów operacyjnych i architektura kombinacje:

    -   Serwer Ubuntu 18.04 x64

    -   Ubuntu Server 16.04 x64

    -   Raspbian Stretch Database dla arm32 (armhf)

**Wymiarowanie urządzenia**

-   Dowolne urządzenie dzięki równoważne wymiary (Procesora/pamięci RAM/Storage/GPU/i itp.) z urządzeniem Raspberry Pi lub nowszej mogą być urządzenia usługi IoT Edge. Jeśli moduł działa tylko w ramach ograniczeń określonych wymiarów, należy określić te ograniczenia w opisie modułu.

**Ustawienia domyślne**

-   Moduł powinien zaczynać się od parametrów domyślnych gotowych dla każdej obsługiwanej platformy (systemu operacyjnego + architecture).

-   Ustawienia konfiguracji powinna być jasno udokumentowane (tagi, zmienne środowiskowe, bliźniacza reprezentacja, trasy) Częścią listy partnerów można zdefiniować opis dla ich modułu, który obsługuje podstawowe kod znaczników HTML lub punktów do zewnętrznej strony sieci Web.

**Przechowywanie wersji**

-   Klienci muszą można zdecydować, czy chcą Autoaktualizacja modułu pochodzące z portalu marketplace albo jeśli mają być użyte dokładna wersja zostały przetestowane. Moduły w portalu Marketplace, należy wykonać tego samego wzorca przechowywania wersji, co środowisko uruchomieniowe usługi IoT Edge. Na przykład:

    -   Stopniowe znacznikami, takimi jak "1.0", może zostać zaktualizowana.

    -   Nie można zaktualizować ze znacznikami wersję pomocniczą, takimi jak "1.3.24".

**Dane telemetryczne**

-   Modułów przy użyciu zestawu SDK usługi IoT moduł musi być ustawiony identyfikator unikatowy moduł przypisany do celów telemetrii w portalu Marketplace. Dzięki temu w portalu Azure Marketplace zidentyfikować liczbę wystąpień modułu, które są uruchomione. Ten unikatowy identyfikator jest przypisana w portalu Marketplace na wprowadzanie nazwy kontenera. Aby ustawić tego identyfikatora, należy użyć metod następujących SDK:
    - [C\#](https://hub.docker.com/_/mysql/)
    - [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Java](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._product_info?view=azure-java-stable)

-   Dla modułów, które nie korzystają z zestawu SDK modułu IoT mniej dokładne informacje są dostępne za pośrednictwem portalu Cloud Partner. Na przykład liczba plików do pobrania.

**Bezpieczeństwo**

-   Kontenery powinny mieć najniższych uprawnieniach dostępu do hosta, jak to możliwe. "Uprzywilejowanych" kontenerów powinna być bardzo rzadko.

-   Partnerzy muszą zachować swoje modułu zabezpieczyć w ramach pomocy technicznej, które zapewniają.

**Aktualizacje**

-   Partnerzy muszą przechowywać ich modułu aktualności i funkcjonalności. One będziesz otrzymywać powiadomienia o wcześniej w przypadku dowolnego przełomowe zmiany zaplanowała IoT Edge w czasie wykonywania.

**Moduł zestawu SDK usługi IoT**

-   Łącznie z zestawu SDK usługi IoT moduł nie jest wymaganiem wstępnym dla certyfikacji.
    Jednak w tym Module zestawu SDK usługi IoT oferują lepsze środowisko użytkownika. Na przykład do obsługi routingu i wysyłanie komunikatów do chmury i tak dalej. Zestaw SDK modułu IoT jest wymagany do Pobierz dane telemetryczne na liczbę wystąpień modułu.

**Subiektywna wymaganie**

-   Musi spełniać co najmniej jeden rzeczywistych przypadków użycia usługi IoT Edge. Na przykład kontener WordPress nie może mieć dołączone, chyba że klient chce używać jej z usługi IoT Edge.

**Wymogi prawne (z zasad AMP)**

-   Moduł musi być zgodne z zasadami i umowy Microsoft Azure Marketplace. Aby uzyskać więcej informacji, zobacz dołączona Umowa wydawcy i [zasad uczestnictwa](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

-   Oprócz umów portalu Azure Marketplace i zasady mogą istnieć dodatkowe wymagania prawne specyficzne dla typu oferty moduł usługi IoT Edge. Ta funkcja jest obecnie w trakcie przeglądu.

-   Moduł musi mieć *warunki użytkowania* i *zasady zachowania poufności informacji*.

-   Moduł powinien mieć również innej powiadomienie, jeśli moduł używa podmiotom.

**Wymagania w zakresie obsługi (z zasad AMP)**

-   Partnerzy są odpowiedzialni za obsługę swoich IoT Edge modułów. Zapewni one pozostawienie dostępne opcje pomocy technicznej podanych w opisie moduł usługi IoT Edge i opcji pomocy technicznej co najmniej jeden jest zawsze dostępny. (Zobacz sekcja 4 umowie wydawcy AMP, aby uzyskać więcej informacji).

**Kategoryzacji**

-   Wszystkie moduły usługi IoT Edge będą wyświetlane w kategorii **Internet rzeczy \>moduł usługi IoT Edge**. To partner o wybranie najlepszej kategorii podrzędnych dla swojego produktu.
