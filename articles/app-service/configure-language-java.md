---
title: Konfigurowanie Windows aplikacje Java — usługa Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować aplikacje w języku Java do uruchamiania na domyślne wystąpienia Windows w usłudze Azure App Service.
keywords: Usługa Azure app service, aplikacji sieci web, windows, oss, języka java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe;cephalin
ms.custom: seodec18
ms.openlocfilehash: efeab014c7d92a6c19d516a121fbc4436925be75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850992"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Konfigurowanie Windows aplikację w języku Java dla usługi Azure App Service

Usługa Azure App Service umożliwia deweloperom szybkie tworzenie, wdrażanie i skalowanie ich Tomcat języka Java lub Java Standard Edition (SE) w pakiecie aplikacji sieci web w pełni zarządzana usługa, systemem Windows. Wdrażanie aplikacji przy użyciu wtyczki usługi Maven w wierszu polecenia lub w edytorach, takich jak IntelliJ, Eclipse lub Visual Studio Code.

Ten przewodnik zawiera podstawowe pojęcia i instrukcje dla deweloperów języka Java przy użyciu usługi aplikacji. Jeśli nie znasz usługi Azure App Service, należy przeczytać za pośrednictwem [Szybki Start Java](app-service-web-get-started-java.md) pierwszy. W odpowiedzi są ogólne pytania dotyczące korzystania z usługi App Service, które nie są specyficzne dla rozwoju języka Java [często zadawane pytania dotyczące aplikacji usługi Windows](faq-configuration-and-management.md).

> [!NOTE]
> Nie możesz znaleźć, czego szukasz? Zobacz [— często zadawane pytania dla usługi OSS Windows](faq-configuration-and-management.md) lub [Przewodnik po konfiguracji Java systemu Linux](containers/app-service-linux-java.md) informacji na temat wdrażania i zabezpieczania aplikacji Java.

## <a name="configuring-tomcat"></a>Konfigurowanie serwera Tomcat

Aby edytować jego Tomcat `server.xml` lub inne pliki konfiguracyjne, najpierw zanotuj Twoja wersja główna serwera Tomcat w portalu.

1. Znajdź katalog macierzysty Tomcat dla danej wersji, uruchamiając `env` polecenia. Wyszukaj zmienną środowiskową, która rozpoczyna się od `AZURE_TOMCAT`i odpowiada używanej wersji głównej. Na przykład `AZURE_TOMCAT85_HOME` wskazuje katalog Tomcat dla serwera Tomcat 8.5.
1. Po zidentyfikowaniu Tomcat katalogu macierzystego dla danej wersji, skopiuj katalog konfiguracji do `D:\home`. Na przykład jeśli `AZURE_TOMCAT85_HOME` miał wartość `D:\Program Files (x86)\apache-tomcat-8.5.37`, będzie pełna ścieżka katalogu konfiguracji skopiowany `D:\home\tomcat\conf`.

Na koniec uruchom ponownie usługę App Service. Przejdź do wdrożeń `D:\home\site\wwwroot\webapps` tak jak wcześniej.

## <a name="java-runtime-statement-of-support"></a>Java runtime oświadczenie zespołu pomocy technicznej

### <a name="jdk-versions-and-maintenance"></a>Wersje zestawu JDK i konserwacji

Interfejsów platformy Azure obsługiwane Java Development Kit (JDK) jest [Zulu](https://www.azul.com/downloads/azure-only/zulu/) dostępne za pośrednictwem [firmy Azul Systems](https://www.azul.com/).

Aktualizacji wersji głównych będzie świadczona poprzez nowe opcje środowiska uruchomieniowego usługi Azure App Service dla Windows. Klienci aktualizacji tych nowszych wersji języka Java, konfigurując ich wdrożenie usługi App Service i są odpowiedzialne za przeprowadzanie testów i zapewnienie ważna aktualizacja odpowiadają ich potrzebom.

Obsługiwane JDK są automatycznie poprawić kwartalnie w styczniu, kwietniu, lipcu i październiku każdego roku.

### <a name="security-updates"></a>Aktualizacje zabezpieczeń

Jak najszybciej po ich udostępnieniu firmy Azul Systems zostaną wydane poprawki i poprawki dla luki w zabezpieczeniach głównych. "Główne" luk w zabezpieczeniach jest zdefiniowanym przez wynik podstawowy 9.0 lub nowszej na [NIST typowe luki w zabezpieczeniach oceniania systemu, wersja 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Ogłoszone jako przestarzałe i wycofania

Jeśli zostanie wycofana obsługiwane środowisko wykonawcze języka Java, deweloperów platformy Azure przy użyciu których to dotyczy środowiska uruchomieniowego otrzymają powiadomienie o wycofaniu co najmniej sześć miesięcy przed środowiska uruchomieniowego została wycofana.

### <a name="local-development"></a>Lokalne programowanie

Deweloperzy mogli przeprowadzić pobieranie produkcji wersji o Azul Zulu Enterprise zestaw JDK potrzeby lokalnego programowania z [witryny pobierania firmy Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Obsługa projektowania

Pomoc techniczna dla [obsługiwanej przez platformę Azure rozwiązanie Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) jest dostępna za pośrednictwem firmy Microsoft, podczas tworzenia na platformie Azure lub [usługi Azure Stack](https://azure.microsoft.com/overview/azure-stack/) z [kwalifikowana planu pomocy technicznej Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Obsługa środowiska uruchomieniowego

Deweloperzy mogą [Otwórz problem](/azure/azure-supportability/how-to-create-azure-support-request) z JDK oprogramowanie Zulu firmy Azul za pośrednictwem pomocy technicznej platformy Azure, jeśli mają one [plan pomocy technicznej kwalifikowaną](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Kolejne kroki

Ten temat zawiera instrukcję środowisko wykonawcze języka Java, pomocy technicznej dla usługi Azure App Service na Windows.

- Aby dowiedzieć się więcej o hostingu aplikacji sieci web za pomocą usługi Azure App Service, zobacz [Omówienie usługi App Service](overview.md).
- Aby uzyskać informacje o używaniu języka Java na programowanie na platformie Azure, zobacz [platforma Azure w Centrum deweloperów języka Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
