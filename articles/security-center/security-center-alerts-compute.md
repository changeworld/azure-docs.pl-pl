---
title: Wykrywanie zagrożeń dla natywnych obliczeń w chmurze w Azure Security Center | Microsoft Docs
description: Ten temat przedstawia natywne alerty obliczeniowe w chmurze dostępne w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: memildin
ms.openlocfilehash: c3fcbadf93ff72f7d2a1dca3b25ace81c9d4f1ae
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202628"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Wykrywanie zagrożeń dla natywnych obliczeń w chmurze w Azure Security Center

Jako dostawca chmury Azure Security Center używa unikatowej widoczności, która musi analizować dzienniki wewnętrzne i identyfikować metodologie ataków na wiele obiektów docelowych. W tym temacie przedstawiono alerty dostępne dla następujących usług platformy Azure:

* [Usługa Azure App Service](#app-services)
* [Azure Container Service](#azure-containers) 

## Azure App Service<a name="app-services"></a>

Security Center używa skali chmury, aby identyfikować ataki ukierunkowane na aplikacje działające przez App Service. Aplikacje sieci Web są wspólne w nowoczesnych sieciach, a osoby atakujące mogą wykrywać te i luki w zabezpieczeniach. Przed skierowaniem do określonych środowisk żądania do aplikacji uruchomionych na platformie Azure przechodzą przez kilka bram, gdzie są one kontrolowane i rejestrowane. Te dane są następnie używane do identyfikowania luk w zabezpieczeniach i ataków oraz do uczenia się nowych wzorców, które będą używane później.

Korzystając z widoczności platformy Azure jako dostawcy chmury, Security Center analizuje App Service dzienników wewnętrznych w celu zidentyfikowania metodologii ataków na wiele obiektów docelowych. Na przykład metodologia obejmuje szerokie skanowanie i rozproszone ataki. Ten typ ataku zwykle pochodzi z małego podzbioru adresów IP i wykazuje wzorce przeszukiwania do podobnych punktów końcowych na wielu hostach. Ataki są wyszukiwaniem zagrożonej strony lub wtyczki i nie mogą być zidentyfikowane z punktu widzenia jednego hosta.

Security Center również ma dostęp do podstawowych piaskownic i maszyn wirtualnych. Wraz z dowodowych pamięci infrastruktura może informować o tym o tym scenariuszu, od nowego ataku rozprowadzonego na komputerach klienckich. W związku z tym Security Center mogą wykrywać ataki na aplikacje sieci Web, które są dłuższe po wykorzystaniu.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Wykryto podejrzane wywołanie w ramach platformy WordPress**|App Service dziennik aktywności wskazuje możliwe działanie iniekcji kodu w zasobie App Service.<br/> To podejrzane działanie przypomina aktywność, która manipuluje kompozycją WordPress w celu obsługi wykonywania kodu po stronie serwera, a następnie bezpośredniego żądania sieci Web w celu wywołania pliku motywu, który został przekierowany. Ten typ działania może być częścią kampanii ataków za pośrednictwem platformy WordPress.|
|**Wykryto połączenie ze stroną internetową z nietypowego adresu IP**|App Service dziennik aktywności wskazuje połączenie z poufną stroną sieci Web na podstawie adresu źródłowego, który nigdy nie jest z nim połączony. Może to wskazywać, że ktoś próbuje przeprowadzić atak na strony administrowania aplikacjami sieci Web. Może być również wynikiem wiarygodnego użytkownika korzystającego z nowego adresu IP.|
|**W analizie zagrożeń znaleziono adres IP połączony z interfejsem FTP Azure App Service.**|App Service Analiza dzienników FTP wykryła połączenie z adresu źródłowego, który został znaleziony w kanale informacyjnym analizy zagrożeń. Podczas tego połączenia użytkownik uzyskał dostęp do wyświetlanych stron.|
|**Wykryto odciskiem palca sieci Web**|App Service dziennik aktywności wskazuje możliwe działanie odciskiem palca sieci Web w zasobie App Service. <br/>To podejrzane działanie jest skojarzone z narzędziem o nazwie Blind słoni. Na serwerach sieci Web odciski palców narzędzia i próby wykrycia zainstalowanych aplikacji i ich wersji. Osoby atakujące często używają tego narzędzia do sondowania aplikacji sieci Web w celu znalezienia luk w zabezpieczeniach.|
|**Wykryto podejrzany dostęp do potencjalnie narażonej strony sieci Web**|Dziennik aktywności App Service wskazuje, że dostęp do strony sieci Web, która jest niezależna. <br/>To podejrzane działanie pochodzi z adresu źródłowego, którego wzorzec dostępu jest podobny do skanera sieci Web. Ten rodzaj działania jest często związany z próbą skanowania sieci przez osobę atakującą w celu uzyskania dostępu do poufnych lub narażonych stron sieci Web.|
|**Plik PHP w folderze przekazywania**|Dziennik aktywności App Service wskazuje, że w folderze przekazywania uzyskano dostęp do podejrzanej strony PHP. <br/>Ten typ folderu zwykle nie zawiera plików PHP. Istnienie tego typu pliku może wskazywać na wykorzystanie zalet dowolnych luk w zabezpieczeniach przekazywania plików.|
|**Próba uruchomienia poleceń systemu Linux w systemie Windows App Service**|Analiza procesów App Service wykryła próbę uruchomienia polecenia systemu Linux na App Service Windows. Ta akcja była uruchomiona przez aplikację sieci Web. Takie zachowanie jest często widoczne podczas kampanii wykorzystujących luki w zabezpieczeniach w typowej aplikacji sieci Web.|
|**Wykryto podejrzane wykonanie w języku PHP**|Dzienniki maszyn wskazują, że jest uruchomiony podejrzany proces PHP. Akcja obejmowała próbę uruchomienia poleceń systemu operacyjnego lub kodu PHP z wiersza polecenia przy użyciu procesu PHP. Chociaż to zachowanie może być wiarygodne, w aplikacjach sieci Web takie zachowanie może wskazywać na złośliwe działania, takie jak próby zainfekowania witryn sieci Web za pomocą powłok internetowych.|
|**Wykonywanie procesu z folderu tymczasowego**|Analiza procesów App Service wykryła wykonywanie procesu z folderu tymczasowego aplikacji. Chociaż to zachowanie może być prawdziwe, w aplikacjach sieci Web takie zachowanie może wskazywać na złośliwe działania.|
|**Wykryto próbę uruchomienia polecenia o wysokim poziomie uprawnień**|Analiza procesów App Service wykryła próbę uruchomienia polecenia wymagającego wysokiego poziomu uprawnień. Polecenie zostało uruchomione w kontekście aplikacji sieci Web. Chociaż to zachowanie może być prawdziwe, w aplikacjach sieci Web takie zachowanie może wskazywać na złośliwe działania.|

> [!NOTE]
> Security Center wykrywanie zagrożeń dla App Service nie jest obecnie dostępne w regionach platformy Azure dla instytucji rządowych i suwerennych.

## Azure Container Service<a name="azure-containers"></a>

Security Center zapewnia wykrywanie zagrożeń w czasie rzeczywistym dla kontenerów na maszynach z systemem Linux na podstawie struktury poddanej inspekcji. Alerty identyfikują kilka podejrzanych działań platformy Docker, takich jak tworzenie kontenera uprzywilejowanego na hoście, wskazanie serwera Secure Shell (SSH) działającego wewnątrz kontenera Docker lub użycie kryptografii wyszukiwarek używa tego. 

Te informacje pozwalają na szybkie rozwiązywanie problemów dotyczących zabezpieczeń i poprawę bezpieczeństwa kontenerów. Oprócz wykrywania systemu Linux Security Center oferuje także analizy, które są bardziej specyficzne dla wdrożeń kontenerów.
