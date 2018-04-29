---
title: Wdrożenia usługi Azure odłączonego decyzje dotyczące stosu Azure zintegrowanych systemów | Dokumentacja firmy Microsoft
description: Określ decyzje dotyczące wdrożeniach z wieloma węzłami połączone Azure Azure stosu dotyczących planowania wdrażania.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 49697a57e59b652fed4997d57bc7ae15cc596cf7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Rozłączona wdrożenia usługi Azure planowania decyzje dotyczące stosu Azure zintegrowanych systemów
Po ustaleniu [jak zintegruje stosu Azure do środowiska chmury hybrydowej](azure-stack-connection-models.md), można następnie zakończyć swoje decyzje dotyczące wdrażania usługi Azure stosu.

Z odłączone od opcji wdrożenia usługi Azure może wdrażanie i użytkowanie stosu Azure bez połączenia z Internetem. Jednak odłączonego wdrożenia jest ograniczona do magazynu tożsamości usługi AD FS i wydajności na podstawie modelu rozliczeń. 

Wybierz tę opcję, jeśli użytkownik:
- Mieć zabezpieczeń lub inne ograniczenia, które wymagają wdrażanie Azure stosu w środowisku, które nie jest połączony z Internetem.
- Chcesz zablokować danych (w tym dane użycia) zostały wysłane do platformy Azure.
- Chcesz użyć stosu Azure wyłącznie jako rozwiązania chmury prywatnej, który został wdrożony do firmowego intranetu i nie są zainteresowane w scenariuszach hybrydowych.

> [!TIP]
> Czasami środowisku tego typu jest również nazywany "podmorski scenariusza".

Odłączonego wdrożenia nie ściśle oznacza, że w scenariuszach maszyny Wirtualnej dzierżawcy hybrydowych nie może połączyć później wystąpienia stosu Azure na platformie Azure. Oznacza to, że masz łączność z Azure podczas wdrażania lub nie chcesz używać usługi Azure Active Directory jako magazyn tożsamości.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Funkcje, które są ograniczony lub jest niedostępna w odłączonych wdrożenia 
Stos Azure została zaprojektowana by najlepiej pracować po podłączeniu do platformy Azure, dlatego należy pamiętać, że istnieją pewne funkcje i możliwości, utrata lub całkowicie niedostępne w trybie rozłączonym. 

|Cecha|Uderzenia w trybie rozłączonym|
|-----|-----|
|Wdrażanie maszyny Wirtualnej za pomocą rozszerzenia usługi Konfiguracja DSC skonfigurować maszyny Wirtualnej po wdrożeniu|Ograniczona - rozszerzenia DSC szuka z Internetem najnowszej wersji platformy WMF.|
|Wdrażanie maszyny Wirtualnej z rozszerzeniem Docker na potrzeby uruchamiania poleceń Docker|Ograniczona — Docker sprawdzi Internet najnowszej wersji i ten test zakończy się niepowodzeniem.|
|Łącza dokumentacji w portalu Azure stosu|Niedostępna — łącza, takie jak uzyskać pomoc, Przekaż opinię szybkiego startu, itp. używające internetowy adres URL nie będzie działać.|
|Alert korygowania/ograniczenie, która odwołuje się przewodnik online korygowania|Niedostępny — żadnych alertów korygowania łączy używające internetowy adres URL nie będzie działać.|
|Syndykacja Marketplace — możliwości, aby wybrać i dodać pakiety galerii bezpośrednio z poziomu portalu Azure Marketplace|Ograniczona — wdrażając stosu Azure w trybie rozłączonym (bez żadnych łączności z Internetem), nie można pobrać elementów marketplace przy użyciu portalu Azure stosu. Można jednak użyć [marketplace zespolonego narzędzia](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) pobierania elementów marketplace na komputerze, na którym ma połączenie z Internetem i przesyła je do środowiska Azure stosu.|
|Zarządzanie wdrożenia stosu Azure za pomocą usługi Azure Active Directory federation kont|Niedostępny — ta funkcja wymaga połączenia do platformy Azure. Zamiast tego należy użyć usług AD FS z lokalnym wystąpieniem usługi Active Directory.|
|App Services|Ograniczona — używanie mogą wymagać dostępu do Internetu dla zaktualizowanej zawartości.|
|Interfejs wiersza polecenia (CLI)|Ograniczona — CLI ma ograniczone funkcje w zakresie uwierzytelniania i dostarczania zasad usługi.|
|Visual Studio — rozwiązania Cloud discovery|Ograniczona — rozwiązania Cloud Discovery albo odnajdzie różne chmury lub nie będą działać w ogóle.|
|Visual Studio – AD FS|Ograniczona — tylko do programu Visual Studio Enterprise obsługuje usługi AD FS.
Telemetria|Niedostępna — dane telemetryczne stosu Azure jako oraz wszelkie pakiety galerii innych firm, które są zależne od danych telemetrycznych.|
|Certyfikaty|Niedostępna — łączność z Internetem jest wymagana w przypadku usług listy odwołania certyfikatów (CRL) i protokołu Online stan certyfikatu (OSCP) w kontekście HTTPS.|
|Key-Vault|Ograniczona — przypadek użycia typowe dla usługi Key Vault jest aplikacja odczyt kluczy tajnych w czasie wykonywania. Dla tej aplikacji wymaga nazwy głównej usługi w katalogu. W usłudze Azure Active Directory normalnych użytkowników (z systemem innym niż administratorzy) są domyślnie można dodawać nazwy główne usług. W usłudze AD (przy użyciu usług AD FS) nie są one. Próg wykluczający to umieszcza w środowisko end-to-end, ponieważ jeden zawsze musi przechodzić przez administratora katalogu, aby dodać dowolnej aplikacji.| 

## <a name="learn-more"></a>Dowiedz się więcej
- Informacje o przypadki użycia, zakupu partnerami i dostawcami sprzętu OEM, zobacz [stosu Azure](https://azure.microsoft.com/overview/azure-stack/) stronę produktu.
- Informacji o plan i dostępności geograficznie stosu Azure zintegrowanych systemów, zobacz oficjalny dokument: [stosu Azure: rozszerzenie Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Aby dowiedzieć się więcej o Microsoft Azure stosu pakowania i cenach [Pobierz PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Kolejne kroki
[Integracja sieci centrum danych](azure-stack-network.md)