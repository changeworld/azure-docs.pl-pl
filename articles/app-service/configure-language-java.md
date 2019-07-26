---
title: Konfigurowanie aplikacji Java dla systemu Windows — Azure App Service | Microsoft Docs
description: Dowiedz się, jak skonfigurować aplikacje Java do uruchamiania w domyślnych wystąpieniach systemu Windows w Azure App Service.
keywords: Azure App Service, Web App, Windows, OSS, Java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: c1ea306d8a6b5c1876ac6a9288820e1592dbfda6
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498526"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Konfigurowanie aplikacji Java systemu Windows dla Azure App Service

Azure App Service umożliwia deweloperom języka Java szybkie tworzenie, wdrażanie i skalowanie aplikacji sieci Web Tomcat lub Java Standard Edition (SE) spakowanych w pełni zarządzanej usługi opartej na systemie Windows. Wdrażaj aplikacje z wtyczkami Maven z poziomu wiersza polecenia lub w edytorach, takich jak IntelliJ, zaćmienie lub Visual Studio Code.

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla deweloperów języka Java korzystających z programu w App Service. Jeśli nie korzystasz z Azure App Service, musisz najpierw przeczytać [Przewodnik Szybki Start dla języka Java](app-service-web-get-started-java.md) . Ogólne pytania dotyczące korzystania z App Service, które nie są specyficzne dla programowania w języku Java, są odpowiedzi na [często zadawane pytania dotyczące App Service systemu Windows](faq-configuration-and-management.md).

> [!NOTE]
> Nie możesz znaleźć, czego szukasz? Aby uzyskać informacje na temat wdrażania i zabezpieczania aplikacji Java, zapoznaj się z tematem [Windows OSS — często zadawane pytania](faq-configuration-and-management.md) lub [Przewodnik po konfiguracji języka Java Linux](containers/configure-language-java.md) .

## <a name="configuring-tomcat"></a>Konfigurowanie Tomcat

Aby edytować Tomcat `server.xml` lub inne pliki konfiguracyjne, najpierw Zanotuj wersję główną Tomcat w portalu.

1. Znajdź katalog macierzysty Tomcat dla swojej wersji, uruchamiając `env` polecenie. Wyszukaj zmienną środowiskową rozpoczynającą się od `AZURE_TOMCAT`i dopasowuje wersję główną. Na przykład `AZURE_TOMCAT85_HOME` wskazuje katalog Tomcat dla Tomcat 8,5.
1. Po zidentyfikowaniu katalogu macierzystego Tomcat dla danej wersji Skopiuj katalog konfiguracji do `D:\home`programu. Na przykład, jeśli `AZURE_TOMCAT85_HOME` ma `D:\Program Files (x86)\apache-tomcat-8.5.37`wartość, Nowa ścieżka `D:\home\apache-tomcat-8.5.37`do skopiowanego katalogu byłaby.

Na koniec uruchom ponownie usługę App Service. Twoje wdrożenia powinny przejść do `D:\home\site\wwwroot\webapps` tak jak wcześniej.

## <a name="java-runtime-statement-of-support"></a>Instrukcja obsługi środowiska uruchomieniowego Java

### <a name="jdk-versions-and-maintenance"></a>JDK wersje i konserwacja

Obsługiwany zestaw Java Development Kit (JDK) platformy Azure jest [Zulu](https://www.azul.com/downloads/azure-only/zulu/) udostępniany przez [systemy Azul](https://www.azul.com/).

Aktualizacje wersji głównej zostaną udostępnione za poorednictwem nowych opcji środowiska uruchomieniowego w Azure App Service dla systemu Windows. Klienci mogą aktualizować te nowsze wersje środowiska Java przez skonfigurowanie wdrożenia App Service i są odpowiedzialne za testowanie i upewnienie się, że główna aktualizacja spełnia ich potrzeby.

Obsługiwane zestawy JDK są automatycznie poprawiane co kwartał w styczniu, kwietniu, lipcu i październiku każdego roku.

### <a name="security-updates"></a>Aktualizacje zabezpieczeń

Poprawki i poprawki dla głównych luk w zabezpieczeniach zostaną wydane, gdy tylko staną się dostępne w systemach Azul. Usterka "główna" jest definiowana przez podstawowy wynik 9,0 lub wyższy w [systemie oceny typowych luk w zabezpieczeniach NIST, wersja 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Wycofanie i wycofanie

Jeśli obsługiwane środowisko uruchomieniowe języka Java zostanie wycofane, deweloperzy platformy Azure korzystający z środowiska uruchomieniowego, którego to dotyczy, otrzymają powiadomienie o wycofaniu z co najmniej sześciu miesięcy przed wycofaniem środowiska uruchomieniowego.

### <a name="local-development"></a>Programowanie lokalne

Deweloperzy mogą pobrać wersję produkcyjną Azul Zulu Enterprise JDK na potrzeby lokalnego opracowywania z [witryny pobierania programu Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Obsługa programowania

Pomoc techniczna dla platformy [Azure Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) jest dostępna w firmie Microsoft podczas tworzenia aplikacji dla platformy azure lub [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) z [wykwalifikowanym planem pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Obsługa środowiska uruchomieniowego

Deweloperzy mogą [otworzyć problem](/azure/azure-supportability/how-to-create-azure-support-request) z usługą Azul Zulu zestawy JDK za pomocą pomocy technicznej systemu Azure, jeśli ma on [wykwalifikowany plan pomocy technicznej](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Kolejne kroki

Ten temat zawiera instrukcję obsługi środowiska uruchomieniowego Java dla Azure App Service w systemie Windows.

- Aby dowiedzieć się więcej na temat hostowania aplikacji sieci Web za pomocą Azure App Service zobacz [App Service Omówienie](overview.md).
- Aby uzyskać informacje na temat programowania w języku Java na platformie Azure, zobacz artykuł [Azure for Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
