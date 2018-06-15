---
title: Azure stosu zintegrowane systemy modelami połączenia | Dokumentacja firmy Microsoft
description: Określ decyzje dotyczące stosu Azure wielowęzłowego dotyczących planowania wdrażania.
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
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: e6c94ef1172ea6380a94d5907c24069ed8c48ff5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29118792"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Azure stosu zintegrowane systemy modelami połączenia
Jeśli interesuje Cię systemu Azure stosu zintegrowane, musisz poznać [kilka kwestii integracji datacenter](azure-stack-datacenter-integration.md) dla wdrożenia stosu Azure określić, jak system zmieści się w centrum danych. Ponadto należy określić dokładnie sposób będzie integracji stosu Azure do środowiska chmury hybrydowej. Ten artykuł zawiera omówienie tych istotnych decyzji, w tym połączeniu z platformą Azure, Magazyn tożsamości i decyzje modelu rozliczeń.

Jeśli zdecydujesz się na wykupienie zintegrowany system, dostawca sprzętu producenta sprzętu (OEM) pomaga przeprowadzają użytkownika przez proces znacznie procesu planowania bardziej szczegółowo. Będą one również wykonywać rzeczywiste wdrożenie.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Wybierz model połączenia wdrożenia stosu Azure
Można wdrożyć stosu Azure albo połączony z Internetem (i na platformie Azure) lub odłączony. Uzyskanie największe korzyści ze stosu Azure, w tym scenariuszy hybrydowych między stosu Azure i platformą Azure, czy chcesz wdrożyć podłączone do platformy Azure. Ten wybór określa, które opcje są dostępne dla magazynu tożsamości (Azure Active Directory lub Active Directory Federation Services) i modelu rozliczeń (zapłacić jako podstawie Użyj rozliczeń lub, w zależności od pojemności rozliczeń) zgodnie z opisem w następujących tabeli i diagramu: 

![Azure wdrożenia stosu i rozliczeń scenariuszy](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> To jest punkt kluczowe decyzje! Wybór Active Directory Federation Services (AD FS) lub Azure Active Directory (Azure AD) jest jednorazowe decyzji, które należy wykonać w czasie wdrażania. Nie można zmienić to później bez ponownego wdrażania całego systemu.  


|Opcje|Połączony na platformie Azure|Odłączony od usługi Azure|
|-----|-----|-----|
|Azure AD|![Obsługiwane](media/azure-stack-connection-models/check.png)| |
|AD FS|![Obsługiwane](media/azure-stack-connection-models/check.png)|![Obsługiwane](media/azure-stack-connection-models/check.png)|
|Na podstawie zużycia rozliczeń|![Obsługiwane](media/azure-stack-connection-models/check.png)| |
|Rozliczania opartego na wydajności|![Obsługiwane](media/azure-stack-connection-models/check.png)|![Obsługiwane](media/azure-stack-connection-models/check.png)|
|Pobierz pakiety aktualizacji bezpośrednio na stosie Azure|![Obsługiwane](media/azure-stack-connection-models/check.png)|  |

Po służący do wdrożenia usługi Azure stosu decydujesz się na model połączenia platformy Azure, decyzje dodatkowych, zależny od połączenia muszą być wprowadzane do magazynu tożsamości i rozliczeń — metoda. 

## <a name="next-steps"></a>Kolejne kroki

[Azure połączonych stosu Azure decyzji dotyczących wdrożenia](azure-stack-connected-deployment.md)

[Azure odłączony decyzji dotyczących wdrożenia stosu Azure](azure-stack-disconnected-deployment.md)
