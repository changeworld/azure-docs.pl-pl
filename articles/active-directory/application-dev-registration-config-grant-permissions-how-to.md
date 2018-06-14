---
title: Jak można udzielić uprawnienia do aplikacji utworzonych niestandardowych | Dokumentacja firmy Microsoft
description: Jak można przyznać uprawnień dostępu do aplikacji utworzonych niestandardowych za pomocą portalu usługi Azure AD lub parametr adresu URL
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 0f13efd5e629bde592a3ab3a3e2db4a22180b5bb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26611665"
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>Jak można udzielić uprawnienia do aplikacji utworzonych niestandardowych

Jeśli chcesz udzielić zgody preemptively w aplikacji lub są uruchomione, wystąpił błąd, który nie wyrażono zgodę dla aplikacji, spróbuj te czynności.

## <a name="how-to-perform-admin-consent-for-your-application"></a>Jak wykonać zgody administratora aplikacji

Skutkuje to udzielania zgody do aplikacji dla wszystkich użytkowników w organizacji.

1. Przejdź do **rejestracji aplikacji** bloku jako **administratora globalnego**, następnie wybierz aplikację.

2. Wybierz **wymagane uprawnienia**, a na koniec trafienie **udzielanie uprawnień** u góry bloku.

Alternatywnie można utworzyć żądania *login.microsoftonline.com* z configs Twojej aplikacji i Dołącz na *& Monituj = admin\_zgody*. Po zarejestrowaniu się przy użyciu poświadczeń administratora usługi, aplikacji udzielono zgody dla wszystkich użytkowników.

## <a name="how-to-force-user-consent-for-your-application"></a>Jak wymusić zgody użytkownika dla aplikacji

* Dołącz do uwierzytelniania żądań *& Monituj = zgody* co wymaga zgody zawsze uwierzytelniają użytkowników końcowych.

## <a name="next-steps"></a>Następne kroki

[Integrowanie aplikacji AzureAD i zgody](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

[Permissioning dla AzureAD w wersji 2.0 i zgody zbieżność aplikacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
