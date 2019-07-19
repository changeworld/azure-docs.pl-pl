---
title: Wykrywanie zagrożeń dla natywnych obliczeń w chmurze w Azure Security Center | Microsoft Docs
description: Ten temat przedstawia natywne alerty obliczeniowe w chmurze dostępne w Azure Security Center. w Azure Security Center.
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
ms.author: v-mohabe
ms.openlocfilehash: 14433806a28e31cef1a278e16cb69e7c9b1a2458
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295843"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Wykrywanie zagrożeń dla natywnych obliczeń w chmurze w Azure Security Center

Jako dostawca chmury Security Center wykorzystuje unikatową widoczność, która musi analizować dzienniki wewnętrzne i identyfikować metodologie ataków na wiele celów. W tym temacie przedstawiono alerty dostępne dla następujących usług platformy Azure:

* [Usługa Azure App Service](#app-services)
* [Containers](#azure-containers) 

## Azure App Service<a name="app-services"></a>

Security Center wykorzystuje skalę chmury, aby identyfikować ataki ukierunkowane na aplikacje klientów uruchamianych przez Azure App Service. Dzięki aplikacjom sieci Web praktycznie w dowolnej nowoczesnej sieci, osoby atakujące mogą wykrywać te i luki w zabezpieczeniach. Przed skierowaniem do określonych środowisk żądania do aplikacji uruchomionych na platformie Azure przechodzą przez kilka bram, gdzie są one kontrolowane i rejestrowane. Te dane są następnie używane do identyfikacji programów wykorzystujących luki w zabezpieczeniach, osoby atakujące i informacje o nowych wzorcach, które będą używane później.

Dzięki zastosowaniu widoczności platformy Azure jako dostawcy chmury Security Center analizuje App Service dzienników wewnętrznych w celu zidentyfikowania metodologii ataków na wiele obiektów docelowych. Na przykład szerokie skanowanie i rozproszone ataki. Ten typ ataku zwykle pochodzi z małego podzestawu adresów IP i wykazuje wzorce przeszukiwania do podobnych punktów końcowych na wielu hostach, wyszukując zagrożoną stronę lub wtyczkę. Ten element można wykryć przy użyciu chmury, ale nie można go zidentyfikować z punktu widzenia jednego hosta.

Security Center również ma dostęp do podstawowych piaskownic i maszyn wirtualnych. Wraz z dowodowych pamięci infrastruktura może informować o tym o tym scenariuszu, od nowego ataku rozprowadzonego na komputerach klienckich. W związku z tym Security Center mogą wykrywać ataki na aplikacje sieci Web, które są dłuższe po wykorzystaniu.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Wykryto podejrzane wywołanie w ramach platformy WordPress**|Azure App Service dziennik aktywności wskazuje możliwe działanie iniekcji kodu w zasobie App Service.<br/> To podejrzane działanie przypomina aktywność, która manipuluje kompozycją WordPress w celu obsługi wykonywania kodu po stronie serwera, a następnie bezpośredniego żądania sieci Web w celu wywołania pliku motywu, który można manipulować. Ten typ działania został napotkany w przeszłości w ramach kampanii ataku za pośrednictwem platformy WordPress.|
|**Wykryto połączenie ze stroną internetową z nietypowego adresu IP**|Azure App Service dziennik aktywności wskazuje połączenie z poufną stroną sieci Web na podstawie adresu źródłowego, który nigdy nie jest z nim połączony. Może to wskazywać, że ktoś próbuje przeprowadzić atak na strony administrowania aplikacjami sieci Web. Może być również wynikiem nowego adresu IP używanego przez uprawnionego użytkownika.|
|**W analizie zagrożeń znaleziono adres IP połączony z interfejsem FTP Azure App Service.**|Azure App Service Analiza dzienników FTP wykryła połączenie z adresu źródłowego, który został znaleziony w kanale informacyjnym analizy zagrożeń. Podczas tego połączenia użytkownik uzyskał dostęp do stron wymienionych poniżej.|
|**Wykryto odciskiem palca sieci Web**|Azure App Service dziennik aktywności wskazuje możliwe działanie odciskiem palca sieci Web w zasobie App Service. <br/>Wykryte podejrzane działania są skojarzone z narzędziem o nazwie Blind słoni. Na serwerach sieci Web odciski palców narzędzia i próby wykrycia zainstalowanych aplikacji i ich wersji. Osoby atakujące często używają tego narzędzia do sondowania aplikacji sieci Web w celu znalezienia luk w zabezpieczeniach.|
|**Wykryto podejrzany dostęp do potencjalnie narażonej strony sieci Web**|Dziennik aktywności Azure App Service wskazuje, że dostęp do strony sieci Web, która jest niezależna. <br/>To podejrzane działanie pochodzi z adresu źródłowego, którego wzorzec dostępu jest podobny do skanera sieci Web. Ten rodzaj działania jest często związany z próbą skanowania sieci przez osobę atakującą w celu uzyskania dostępu do poufnych lub narażonych stron sieci Web.|
|**Plik PHP w folderze przekazywania**|Dziennik aktywności Azure App Service wskazuje, że dostęp do podejrzanej strony PHP znajduje się w folderze przekazywania. <br/>Ten typ folderu zwykle nie zawiera plików PHP. Istnienie tego typu pliku może wskazywać na wykorzystanie zalet dowolnych luk w zabezpieczeniach przekazywania plików.|
|**Próba uruchomienia poleceń systemu Linux w systemie Windows App Service**|Analiza procesów App Service wykryła próbę uruchomienia polecenia systemu Linux na App Service Windows. Ta akcja była uruchomiona przez aplikację sieci Web. Takie zachowanie jest często widoczne podczas kampanii wykorzystujących luki w zabezpieczeniach w typowej aplikacji sieci Web.|
|**Wykryto podejrzane wykonanie w języku PHP**|Dzienniki maszyn wskazują, że jest uruchomiony podejrzany proces PHP. Akcja obejmowała próbę uruchomienia poleceń systemu operacyjnego lub kodu PHP z wiersza polecenia przy użyciu procesu PHP. Chociaż to zachowanie może być wiarygodne, w aplikacjach sieci Web takie zachowanie jest również zaobserwowane w złośliwych działaniach, takich jak próby zainfekowania witryn internetowych za pomocą powłoki sieci Web.|
|**Wykonywanie procesu z folderu tymczasowego**|Analiza procesów App Service wykryła wykonywanie procesu z folderu tymczasowego aplikacji. Chociaż to zachowanie może być wiarygodne, w aplikacjach sieci Web to zachowanie jest również zaobserwowane w złośliwych działaniach.|
|**Wykryto próbę uruchomienia polecenia o wysokim poziomie uprawnień**|Analiza procesów App Service wykryła próbę uruchomienia polecenia wymagającego wysokiego poziomu uprawnień. Polecenie zostało uruchomione w kontekście aplikacji sieci Web. Chociaż to zachowanie może być wiarygodne, w aplikacjach sieci Web to zachowanie jest również zaobserwowane w złośliwych działaniach.|

> [!NOTE]
> Security Center wykrywanie zagrożeń dla App Service nie jest obecnie dostępne w regionach platformy Azure dla instytucji rządowych i suwerennych.

Aby uzyskać więcej informacji na temat alertów dotyczących wykrywania zagrożeń App Service, odwiedź stronę ochrona App Service za pomocą Azure Security Center i zapoznaj się z tematem Włączanie monitorowania i ochrony obciążeń App Service.

## Opakowania<a name="azure-containers"></a>

Security Center zapewnia wykrywanie zagrożeń w czasie rzeczywistym dla kontenerów na maszynach z systemem Linux na podstawie struktury poddanej inspekcji. Alerty identyfikują kilka podejrzanych działań Docker, takich jak tworzenie kontenera uprzywilejowanego na hoście, wskazanie serwera Secure Shell (SSH) działającego wewnątrz kontenera Docker lub użycie kryptografii wyszukiwarek używa tego. Te informacje pozwalają na szybkie rozwiązywanie problemów dotyczących zabezpieczeń i poprawę bezpieczeństwa kontenerów. Oprócz wykrywania systemu Linux Security Center oferuje także analizy, które są bardziej specyficzne dla wdrożeń kontenerów.
