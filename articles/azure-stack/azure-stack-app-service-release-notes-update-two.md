---
title: Usługa App Service w usłudze Azure Stack aktualizacji 2 — informacje o wersji | Dokumentacja firmy Microsoft
description: Dowiedz się o nowościach w aktualizacji, dwie usługi App Service w usłudze Azure Stack, znanych problemów i umożliwiające pobranie aktualizacji.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: sethm
ms.lastreviewed: 05/18/2018
ms.openlocfilehash: 98d084794accb99e0fecac85f7de861ec35bc15e
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446028"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>Usługa App Service w usłudze Azure Stack update 2 — informacje o wersji

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Tych informacjach o wersji opisano ulepszeń i poprawek w usłudze Azure App Service, Azure Stack w aktualizacji 2 i znanych problemach. Znane problemy są podzielone na problemy z bezpośrednio do wdrożenia, proces aktualizacji i problemy z kompilacją (po instalacji).

> [!IMPORTANT]
> Dotyczą aktualizacji 1804 system zintegrowany z usługi Azure Stack można też wdrażać najnowszy zestaw Azure Stack development kit przed wdrożeniem usługi Azure App Service 1.2.
>
>

## <a name="build-reference"></a>Dokumentacja kompilowania

App Service na numer kompilacji programu Azure Stack Update 2 to **72.0.13698.10**

### <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> Teraz wymagają nowych wdrożeń usługi Azure App Service w usłudze Azure Stack [certyfikat uniwersalny tematu z trzema](azure-stack-app-service-before-you-get-started.md#get-certificates) ze względu na ulepszenia w taki sposób, w jaki logowanie Jednokrotne do Kudu jest teraz obsługiwane w usłudze Azure App Service. Nowy temat jest  **\*. sso.appservice.\< region\>.\< nazwa_domeny\>.\< rozszerzenie\>**
>
>

Zapoznaj się [dokumentacji przed rozpoczęciem](azure-stack-app-service-before-you-get-started.md) przed rozpoczęciem wdrażania.

### <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki

Usługa Azure App Service w usłudze Azure Stack Update 2 obejmuje następujące ulepszenia i poprawki:

- Aktualizacje **dzierżawy usługi aplikacji, administrator, portali funkcje i narzędzia Kudu**. Zgodne z wersją zestawu SDK usługi Azure Stack Portal.

- Aktualizacje **środowisko uruchomieniowe usługi Azure Functions** do **v1.0.11612**.

- Aktualizacje do podstawowej usługi, aby zwiększyć niezawodność i komunikatów o włączenie łatwiejsze Diagnostyka typowe problemy.

- **Aktualizacje do narzędzi i struktur aplikacji na następujących**:
  - Added .NET Framework 4.7.1
  - Dodano **Node.JS** wersji:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Dodano **NPM** wersji:
    - 5.6.0
  - Zaktualizowane składniki platformy .NET Core, aby były zgodne z usługi Azure App Service w chmurze publicznej.
  - Zaktualizowano Kudu

- Automatycznej wymiany wdrożenia gniazd funkcję - [Konfigurowanie automatycznej wymiany](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap)

- Testowanie w produkcji funkcję - [wprowadzenie do testowania w produkcji](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Azure Functions Proxies enabled - [pracować z serwerów proxy usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-proxies)

- Dodaje rozszerzenia administratora usługi App Service obsługuje środowiska użytkownika dla:
  - Obrót wpisu tajnego
  - Rotacja certyfikatu
  - Obrót poświadczeń systemu
  - Obracanie parametrów połączeń

### <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)

- Pracownicy są nie można nawiązać połączenia z serwerem plików po wdrożeniu usługi App Service w istniejącej sieci wirtualnej i serwer plików jest dostępna tylko w sieci prywatnej.

Jeśli wybierzesz do wdrożenia w istniejącej sieci wirtualnej i wewnętrzny adres IP, aby nawiązać połączenie z serwerem plików, należy dodać regułę zabezpieczeń dla ruchu wychodzącego włączanie ruchu SMB między podsieci procesów roboczych i serwera plików. Aby to zrobić, przejdź do WorkersNsg w portalu administracyjnym i dodawanie reguły zabezpieczeń dla ruchu wychodzącego z następującymi właściwościami:
* Źródło: Dowolne
* Zakres portów źródłowych: *
* Miejsce docelowe: Adresy IP
* Docelowy zakres adresów IP: Zakres adresów IP dla serwera plików
* Zakres portów docelowych: 445
* Protokół: TCP
* Akcja: Zezwalaj
* Priorytet: 700
* Nazwa: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Znane problemy dotyczące administratorów chmury, działania usługi Azure App Service w usłudze Azure Stack

Zapoznaj się z dokumentacją w [informacje o wersji usługi Azure Stack w wersji 1804](azure-stack-update-1804.md)

## <a name="next-steps"></a>Kolejne kroki

- Omówienie usługi Azure App Service, zobacz [usługi Azure App Service w usłudze Azure Stack — omówienie](azure-stack-app-service-overview.md).
- Aby uzyskać więcej informacji na temat przygotowywania do wdrożenia usługi App Service w usłudze Azure Stack, zobacz [przed rozpoczęciem pracy z usługą App Service w usłudze Azure Stack](azure-stack-app-service-before-you-get-started.md).
