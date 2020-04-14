---
title: Rozwiązywanie typowych błędów uwierzytelniania | Azure Marketplace
description: Zapewnia pomoc w przypadku typowych błędów uwierzytelniania podczas korzystania z interfejsów API portalu partnerów w chmurze.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: d8fd1eb4bef987b4a8605e4be780512a914ec8b5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255995"
---
# <a name="troubleshooting-common-authentication-errors"></a>Rozwiązywanie typowych błędów uwierzytelniania

> [!NOTE]
> Interfejsy API portalu partnerów w chmurze są zintegrowane z centrum partnerów i będą nadal działać po migracji ofert do Centrum partnerów. Integracja wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [aplikacji Cloud Partner Portal API Reference,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) aby upewnić się, że kod będzie nadal działać po migracji do Centrum partnerów.

Ten artykuł zawiera pomoc dotyczącą typowych błędów uwierzytelniania podczas korzystania z interfejsów API portalu partnerów w chmurze.

## <a name="unauthorized-error"></a>Nieautoryzowany błąd

Jeśli konsekwentnie pojawia `401 unauthorized` się błędy, sprawdź, czy masz prawidłowy token dostępu.  Jeśli jeszcze tego nie zrobiono, utwórz podstawową aplikację usługi Azure Active Directory (Azure AD) i jednostkę usługi zgodnie z opisem w [obszarze Użyj portalu, aby utworzyć aplikację i jednostkę usługi Azure Active Directory, która może uzyskać dostęp do zasobów.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) Następnie użyj aplikacji lub prostego żądania HTTP POST, aby zweryfikować swój dostęp.  W celu uzyskania tokenu dostępu zostanie podany identyfikator dzierżawy, identyfikator aplikacji, identyfikator obiektu i klucz tajny, jak pokazano na poniższej ilustracji:

![Rozwiązywanie problemów z błędem 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Błąd „Niedozwolone”

Jeśli `403 forbidden` zostanie wyświetlony błąd, upewnij się, że do konta wydawcy dodano właściwą jednostkę usługi w portalu cloud partner.
Wykonaj kroki opisane na stronie [Wymagania wstępne,](./cloud-partner-portal-api-prerequisites.md) aby dodać jednostkę usługi do portalu.

Jeśli dodano właściwego podmiotu usługi, sprawdź wszystkie inne informacje. Należy zwrócić szczególną uwagę na identyfikator obiektu wprowadzony w portalu. Istnieją dwa identyfikatory obiektów na stronie rejestracji aplikacji usługi Azure Active Directory i należy użyć lokalnego identyfikatora obiektu. Prawidłową wartość można znaleźć, przechodząc do strony **Rejestracje aplikacji** i klikając nazwę aplikacji w obszarze **Zarządzana aplikacja w katalogu lokalnym**. Spowoduje to przejście do właściwości lokalnych aplikacji, gdzie można znaleźć poprawny identyfikator obiektu na stronie **Właściwości,** jak pokazano na poniższym rysunku. Upewnij się również, że używasz poprawnego identyfikatora wydawcy podczas dodawania jednostki usługi i wywołania interfejsu API.

![Rozwiązywanie problemów z błędem 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
