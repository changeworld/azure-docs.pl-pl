---
title: Usługa aplikacji Azure stosu zaktualizować informacje o wersji 2 | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w aktualizacji dwie usługi aplikacji Azure stosu, znane problemy i pobierania aktualizacji.
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
ms.date: 05/18/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 8e1790b7d0b3a210a9142fc8580ff8ed4d64311c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360393"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>Usługi aplikacji — informacje o wersji update 2 Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Te informacje o wersji opisano ulepszeń i poprawek w usłudze Azure App Service na Azure stosu Update 2 i znanych problemów. Znane problemy są podzielone na problemy z bezpośrednio do wdrożenia, proces aktualizacji i problemy z kompilacją (po instalacji).

> [!IMPORTANT]
> Zastosowanie aktualizacji 1804 systemu Azure stosu zintegrowane lub wdrożyć najnowszy zestaw deweloperski stosu Azure przed wdrożeniem usługi Azure App Service 1.2.
>
>

## <a name="build-reference"></a>Tworzenie odwołania

Usługa aplikacji Azure stosu Update 2 numer kompilacji jest **72.0.13698.10**

### <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> Nowe wdrożenia usługi Azure App Service na stosie Azure wymaga [certyfikat uniwersalny podmiotu trzech](azure-stack-app-service-before-you-get-started.md#get-certificates) ze względu na ulepszenia w taki sposób, w jaki usługa rejestracji Jednokrotnej dla Kudu teraz obsługiwane w usłudze Azure App Service. Nowy temat jest  **\*. sso.appservice.\< region\>.\< DomainName\>.\< rozszerzenia\>**
>
>

Zapoznaj się [przed rozpoczęciem pracy dokumentacji](azure-stack-app-service-before-you-get-started.md) przed rozpoczęciem wdrażania.

### <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki

Usługa aplikacji Azure na Azure stosu Update 2 zawiera następujące ulepszenia i poprawki:

- Aktualizacje **dzierżawa usługi aplikacji, administrator, portali funkcje i narzędzia Kudu**. Spójne z wersją zestawu SDK usługi Azure stosu portalu.

- Aktualizacje usługi podstawowej, aby zwiększyć niezawodność i błąd wiadomości włączenie łatwiejsze Diagnostyka typowych problemów.

- **Aktualizacje następujące struktury aplikacji i narzędzi**:
  - Dodano .net Framework 4.7.1
  - Dodaje **Node.JS** wersji:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Dodaje **NPM** wersji:
    - 5.6.0
  - Zaktualizowano .net podstawowe składniki, aby były spójne z usługi aplikacji Azure w chmurze publicznej.
  - Zaktualizowany aparat Kudu

- Swap automatycznego wdrażania gniazd włączona — funkcja [Konfigurowanie automatycznej wymiany](https://docs.microsoft.com/azure/app-service/web-sites-staged-publishing#configure-auto-swap)

- Testowanie w produkcji funkcja jest włączona - [wprowadzenie do testowania w środowisku produkcyjnym](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Azure proxy funkcje włączone - [pracować z serwerów proxy funkcji platformy Azure](https://docs.microsoft.com/en-us/azure/azure-functions/functions-proxies)

- Dodane rozszerzenia administratora usługi aplikacji środowiska użytkownika obsługują:
  - Tajny obrotu
  - Obracanie certyfikatu
  - Obracanie poświadczeń systemu
  - Obracanie ciąg połączenia

### <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)

- Nie można osiągnąć serwera plików, gdy wdrażana jest usługa aplikacji w ramach istniejącej sieci wirtualnej i serwer plików jest dostępna w sieci prywatnej tylko są pracowników.

Jeśli wybierzesz do wdrożenia w ramach istniejącej sieci wirtualnej i wewnętrzny adres IP, aby połączyć się z serwerem plików, należy dodać reguły zabezpieczeń dla ruchu wychodzącego, włączanie ruchu SMB między podsieci procesu roboczego i serwera plików. Aby to zrobić, przejdź do WorkersNsg w portalu administracyjnym i dodawanie reguły zabezpieczeń dla ruchu wychodzącego z następującymi właściwościami:
 * Źródło: wszystkie
 * Zakres portów źródłowych: *
 * Miejsce docelowe: Adresy IP
 * Docelowy zakres adresów IP: zakres adresów IP dla serwera plików
 * Zakres portów docelowych: 445
 * Protokół: TCP
 * Akcja: Zezwalaj
 * Priorytet: 700
 * Nazwa: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Znane problemy dotyczące działania usługi Azure App Service na stosie Azure Administratorzy chmury

Zapoznaj się z dokumentacją w [1804 stosu Azure informacje o wersji](azure-stack-update-1804.md)

## <a name="next-steps"></a>Kolejne kroki

- Omówienie usługi Azure App Service, zobacz [usłudze Azure App Service na stos Azure omówienie](azure-stack-app-service-overview.md).
- Aby uzyskać więcej informacji dotyczących sposobu przygotowania do wdrożenia usługi aplikacji Azure stosu, zobacz [przed rozpoczęciem pracy z usługi aplikacji Azure stosu](azure-stack-app-service-before-you-get-started.md).
