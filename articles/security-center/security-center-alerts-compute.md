---
title: Wykrywanie dla natywne możliwości obliczeniowe chmury w usłudze Azure Security Center zagrożeń | Dokumentacja firmy Microsoft
description: Ten temat przedstawia informacje o chmurze alertów natywne możliwości obliczeniowe dostępnych w usłudze Azure Security Center. w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: bc3cb66d43e71777e06c6bd63dcff35e2ff19df8
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571688"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Wykrywanie zagrożeń dla chmury natywne możliwości obliczeniowe w usłudze Azure Security Center

Jako dostawca chmury usługa Security Center wykorzystuje unikatowy wgląd, które analizowanie dzienników wewnętrzny i zidentyfikować metodologii atak na wiele obiektów docelowych. W tym temacie przedstawiono alerty, które są dostępne dla następujących usług platformy Azure:

* [Usługa Azure App Service](#app-services)
* [Containers](#azure-containers) 

## Usługa Azure App Service <a name="app-services"></a>

Usługa Security Center wykorzystuje skalowalność chmury do identyfikowania ataków przeznaczonych dla klientów działających w usłudze Azure App Service. Za pomocą aplikacji sieci web jest praktycznie w dowolnej nowoczesnej sieci osoby atakujące sondowania znaleźć je i luki. Zanim zostanie skierowany do konkretnych środowisk, żądania do aplikacji działających na platformie Azure przechodzą przez kilka bram, gdzie są kontrolowane i rejestrowane. Te dane są następnie używane do identyfikowania luki w zabezpieczeniach, osoby atakujące i dowiedzieć się, nowych wzorców, które będą używane później.

Dzięki wykorzystaniu widoczność, takiej jak dostawca chmury platformy Azure, usługa Security Center analizuje dzienniki wewnętrzny usługi App Service, aby zidentyfikować metodologii atak na wiele obiektów docelowych. Na przykład powszechne skanowanie i atakami rozproszonej. Ten rodzaj ataku zazwyczaj pochodzą z małego podzbioru adresów IP i wykazuje wzorców przeszukiwania podobne punkty końcowe na wielu hostach, wyszukując narażone strony lub wtyczki. Może zostać wykryte przy użyciu chmury, ale nie można zidentyfikować z punktu widzenia jednego hosta.

Usługa Security Center ma także dostęp do podstawowych maszyn wirtualnych i piaskownic. Wraz z dowodowych pamięci infrastruktury można stanie zakomunikować wszystkiego, od nowych ataku w środowisku naturalnym do kompromisów w przypadku klientów maszyn. Security Center może wykryć ataków na długi dla aplikacji sieci web, po wykorzystaniu w czasie.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Podejrzane wywołania motyw WordPress wykryto**|Dziennik aktywności usługi Azure App Service wskazuje działania iniekcji kodu możliwe zasobu usługi App Service.<br/> To podejrzane działanie przypomina działania, które obsługuje motyw WordPress do obsługi wykonywania kodu, następuje żądania sieci web bezpośrednie, aby wywołać plik motywu manipulować po stronie serwera. Tego rodzaju działaniach został napotkany w przeszłości w ramach kampanii ataku za pośrednictwem WordPress.|
|**Połączenie do strony sieci web z wykryto nietypowe adres IP**|Dziennik aktywności usługi Azure App Service wskazuje połączenie poufnych strony sieci web z adresu źródłowego, który nigdy nie nawiązano połączenia przed. Może to oznaczać, że ktoś próbuje ataku siłowego na strony administracyjnej aplikacji sieci Web. Może być również wynikiem nowy adres IP używany przez wiarygodnego użytkownika.|
|**Adres IP, który jest podłączony do interfejsu FTP usługi Azure App Service została znaleziona w analizy zagrożeń**|Usługi Azure analysis dzienników aplikacji usługi FTP wykrył połączenie z adresu źródłowego, który został znaleziony w analizy zagrożeń, źródła danych. Podczas tego połączenia dostępu użytkownika do strony wymienione poniżej.|
|**Web odcisków wykryto**|Dziennik aktywności usługi Azure App Service wskazuje sieci web możliwe odcisków działania zasobu usługi App Service. <br/>Ta wykryto podejrzaną aktywność wiążą się z narzędzia o nazwie niewidomych syna. Narzędzie odciski palców serwerów sieci web i próbuje wykryć zainstalowanych aplikacji i ich wersje. Osoby atakujące często używają tego narzędzia do badania aplikacji sieci web można znaleźć luki w zabezpieczeniach.|
|**Podejrzane dostępu do potencjalnie zagrożone strony sieci web wykrył**|Dziennik aktywności usługi Azure App Service wskazuje uzyskano strony sieci web, który wydaje się być wrażliwe. <br/>To podejrzane działanie pochodzi z adresu źródłowego, w których wzorzec dostępu do przypomina, skaner sieci web. Tego rodzaju działalności, często jest skojarzony z próbą przez osobę atakującą do skanowania sieci i spróbuj uzyskać dostęp do poufnych lub narażone stron sieci web.|
|**PHP z plikiem w folderze przekazywania**|Dziennik aktywności usługi Azure App Service wskazuje, że coś uzyskiwał dostęp do podejrzanych strony PHP znajduje się w folderze przekazywania. <br/>Tego rodzaju folder nie zawiera zazwyczaj pliki w języku PHP. Obecność pliku tego typu może wskazywać wykorzystanie zalet luk w zabezpieczeniach dowolnego pliku przekazywania.|
|**Próba uruchomienia polecenia systemu Linux w usłudze aplikacji Windows**|Analiza procesów usługi App Service wykrył próbę uruchomienia polecenia systemu Linux w usłudze aplikacji Windows. Ta akcja została uruchomiona przez aplikację sieci web. To zachowanie jest często występujące podczas kampanie, które są luki w zabezpieczeniach w typowych aplikacji sieci web.|
|**Wykryto podejrzane wykonanie PHP**|Dzienniki maszyny wskazują, że podejrzanego procesu PHP jest uruchomiona. Akcja się próba uruchomienia poleceń systemu operacyjnego lub kodu w języku PHP z poziomu wiersza polecenia przy użyciu procesu PHP. To zachowanie, mogą być uzasadnione, w aplikacjach sieci web to zachowanie jest również obserwowane w przypadku złośliwych działań, takich jak prób do infekowania witryn sieci Web za pomocą powłoki sieci web.|
|**Wykonanie procesu z folderu tymczasowego**|Analiza procesów usługi App Service wykrył wykonanie procesu z folderu tymczasowego aplikacji. To zachowanie, mogą być uzasadnione, w aplikacjach sieci web to zachowanie jest również obserwowane w przypadku złośliwych działań.|
|**Podjęcie próby uruchomienia polecenia wysokim poziomie uprawnień wykryto**|Analiza procesów usługi App Service wykrył podjęcie próby uruchomienia polecenia, który wymaga wysokim poziomem uprawnień. Polecenie został uruchomiony w kontekście aplikacji sieci web. To zachowanie, mogą być uzasadnione, w aplikacjach sieci web to zachowanie jest również obserwowane w przypadku złośliwych działań.|

> [!NOTE]
> Wykrywanie zagrożeń Centrum zabezpieczeń dla usługi App Service nie jest obecnie dostępna w regionach należących do suwerennej chmury i platformy Azure dla instytucji rządowych.

Aby uzyskać więcej informacji na temat wykrywania zagrożeń usługi App Service alerty odwiedź ochrony usługi App Service z usługą Azure Security Center i Dowiedz się, jak włączyć monitorowanie i ochronę obciążeń usługi App Service.

## Kontenery <a name="azure-containers"></a>

Usługa Security Center zapewnia wykrywanie zagrożeń w czasie rzeczywistym dla kontenerów na maszynach z systemem Linux w ramach struktury wykorzystują. Alerty zidentyfikować kilka podejrzanych działań platformy Docker, takich jak tworzenie uprzywilejowanych kontenera na hoście, wskazanie serwera protokołu Secure Shell (SSH) uruchomionej w kontenerze platformy Docker lub korzystanie z usług kryptograficznych wyszukiwarek. Te informacje pozwalają na szybkie rozwiązywanie problemów dotyczących zabezpieczeń i poprawę bezpieczeństwa kontenerów. Oprócz Linux wykrywania zagrożeń usługa Security Center oferuje również analizy, które są bardziej szczegółowe w przypadku wdrożeń kontenerów.
