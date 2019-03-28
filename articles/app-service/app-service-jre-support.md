---
title: Usługa Azure App Service dla obsługi środowiska uruchomieniowego Java Windows
description: Ten temat zawiera instrukcję środowisko wykonawcze języka Java, pomocy technicznej dla usługi Azure App Service na Windows.
author: bmitchell287
ms.author: brendm; joshuapa
ms.date: 3/22/2019
ms.topic: article
ms.service: app-service
manager: dougE
ms.openlocfilehash: ab0e9b9f272108745b629d2cb284f6c5ab52c76f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58523043"
---
# <a name="java-runtime-statement-of-support-for-app-service-on-windows"></a>Java runtime oświadczenie zespołu pomocy technicznej w usłudze App Service w Windows

## <a name="jdk-versions-and-maintenance"></a>Wersje zestawu JDK i konserwacji

Interfejsów platformy Azure obsługiwane Java Development Kit (JDK) jest [Zulu](https://www.azul.com/downloads/azure-only/zulu/) dostępne za pośrednictwem [firmy Azul Systems](https://www.azul.com/).

Aktualizacji wersji głównych będzie świadczona poprzez nowe opcje środowiska uruchomieniowego usługi Azure App Service dla Windows. Klienci aktualizacji tych nowszych wersji języka Java, konfigurując ich wdrożenie usługi App Service i są odpowiedzialne za przeprowadzanie testów i zapewnienie ważna aktualizacja odpowiadają ich potrzebom.

Obsługiwane JDK są automatycznie poprawić kwartalnie w styczniu, kwietniu, lipcu i październiku każdego roku.

## <a name="security-updates"></a>Aktualizacje zabezpieczeń

Jak najszybciej po ich udostępnieniu firmy Azul Systems zostaną wydane poprawki i poprawki dla luki w zabezpieczeniach głównych. "Główne" luk w zabezpieczeniach jest zdefiniowanym przez wynik podstawowy 9.0 lub nowszej na [NIST typowe luki w zabezpieczeniach oceniania systemu, wersja 2](https://nvd.nist.gov/cvss.cfm).

## <a name="deprecation-and-retirement"></a>Ogłoszone jako przestarzałe i wycofania

Jeśli zostanie wycofana obsługiwane środowisko wykonawcze języka Java, deweloperów platformy Azure przy użyciu których to dotyczy środowiska uruchomieniowego otrzymają powiadomienie o wycofaniu co najmniej sześć miesięcy przed środowiska uruchomieniowego została wycofana.

## <a name="local-development"></a>Lokalne programowanie

Deweloperzy mogli przeprowadzić pobieranie produkcji wersji o Azul Zulu Enterprise zestaw JDK potrzeby lokalnego programowania z [witryny pobierania firmy Azul](https://www.azul.com/downloads/azure-only/zulu/).

## <a name="development-support"></a>Obsługa projektowania

Pomoc techniczna dla [obsługiwanej przez platformę Azure rozwiązanie Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) jest dostępna za pośrednictwem firmy Microsoft, podczas tworzenia na platformie Azure lub [usługi Azure Stack](https://azure.microsoft.com/overview/azure-stack/) z [kwalifikowana planu pomocy technicznej Azure](https://azure.microsoft.com/support/plans/).

## <a name="runtime-support"></a>Obsługa środowiska uruchomieniowego

Deweloperzy mogą [Otwórz problem](/azure/azure-supportability/how-to-create-azure-support-request) z JDK oprogramowanie Zulu firmy Azul za pośrednictwem pomocy technicznej platformy Azure, jeśli mają one [plan pomocy technicznej kwalifikowaną](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Kolejne kroki

Ten temat zawiera instrukcję środowisko wykonawcze języka Java, pomocy technicznej dla usługi Azure App Service na Windows.
- Aby dowiedzieć się więcej o hostingu aplikacji sieci web za pomocą usługi Azure App Service, zobacz [Omówienie usługi App Service](overview.md).
- Aby uzyskać informacje o używaniu języka Java na programowanie na platformie Azure, zobacz [platforma Azure w Centrum deweloperów języka Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
