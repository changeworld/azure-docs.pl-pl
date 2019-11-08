---
title: Rozwiązywanie typowych błędów uwierzytelniania | Portal Azure Marketplace
description: Zapewnia pomoc dotyczącą typowych błędów uwierzytelniania podczas korzystania z portal Cloud Partner interfejsów API.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 0990e9aedf17f6d4ad01e4911e47efd60001f3d7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827362"
---
# <a name="troubleshooting-common-authentication-errors"></a>Rozwiązywanie typowych błędów uwierzytelniania

Ten artykuł zawiera informacje o typowych błędach uwierzytelniania podczas korzystania z portal Cloud Partner interfejsów API.

## <a name="unauthorized-error"></a>Nieautoryzowany błąd

Jeśli stale pojawiają się błędy `401 unauthorized`, sprawdź, czy masz prawidłowy token dostępu.  Jeśli jeszcze tego nie zrobiono, Utwórz podstawową aplikację Azure Active Directory (Azure AD) i nazwę główną usługi zgodnie z opisem w temacie [Korzystanie z portalu, aby utworzyć aplikację Azure Active Directory i nazwę główną usługi, które mogą uzyskiwać dostęp do zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Następnie do zweryfikowania dostępu Użyj aplikacji lub prostego żądania POST protokołu HTTP.  W celu uzyskania tokenu dostępu zostanie uwzględniony identyfikator dzierżawy, identyfikator aplikacji, identyfikator obiektu i klucz tajny, jak pokazano na poniższej ilustracji:

![Rozwiązywanie problemów z błędem 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Błąd zabroniony

Jeśli wystąpi błąd `403 forbidden`, upewnij się, że poprawna jednostka usługi została dodana do konta wydawcy w portal Cloud Partner.
Postępuj zgodnie z instrukcjami na stronie [wymagania wstępne](./cloud-partner-portal-api-prerequisites.md) , aby dodać nazwę główną usługi do portalu.

Jeśli została dodana poprawna jednostka usługi, sprawdź wszystkie pozostałe informacje. Zwróć szczególną uwagę na identyfikator obiektu wprowadzony w portalu. Na stronie rejestracji aplikacji Azure Active Directory znajdują się dwa identyfikatory obiektów i należy użyć lokalnego identyfikatora obiektu. Poprawną wartość można znaleźć, przechodząc do strony **rejestracje aplikacji** aplikacji i klikając nazwę aplikacji **w obszarze zarządzana aplikacja w katalogu lokalnym**. Spowoduje to przejście do właściwości lokalnych aplikacji, gdzie można znaleźć prawidłowy identyfikator obiektu na stronie **Właściwości** , jak pokazano na poniższej ilustracji. Ponadto upewnij się, że podczas dodawania nazwy głównej usługi i wywołania interfejsu API używasz poprawnego identyfikatora wydawcy.

![Rozwiązywanie problemów z błędem 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
