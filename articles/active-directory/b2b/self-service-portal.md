---
title: Zapisywanie się do portalu współpracy B2B usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Współpraca B2B usługi Azure Active Directory wspiera relacje między firmami, umożliwiając partnerom biznesowym selektywne uzyskiwanie dostępu do Twoich aplikacji firmowych
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 94001b005a883c172cab279029b47ac1ad0c0de5
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645049"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portal samoobsługowy programu rejestracji współpracy B2B usługi Azure AD

Klienci umożliwiają wykonywanie wielu z wbudowanymi funkcjami, które są udostępniane za pośrednictwem [witryny Azure portal](https://portal.azure.com) i [panelu dostępu do aplikacji](https://myapps.microsoft.com) dla użytkowników końcowych. Jednak może być konieczne dostosowanie przepływ pracy dołączania dla użytkowników B2B do potrzeb Twojej organizacji. Możesz to zrobić za pomocą [zaproszenie interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Jako organizacji zapraszającej możesz nie znać wcześniej, kim są poszczególne zewnętrznych współpracowników, którzy potrzebują dostępu do zasobów. Trzeba wprowadzić użytkowników z firm partnerskich, aby się zarejestrować się za pomocą zestawu zasad, którymi steruje jako organizacji zapraszającej. Ten scenariusz jest możliwy za pośrednictwem interfejsów API. Brak [przykładowy projekt w witrynie GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) , właśnie to robi.

Ten projekt GitHub pokazuje, jak organizacje mogą używać interfejsów API w celu zapewnienia oparte na zasadach, Samoobsługowe możliwości zapisywania do zaufanych partnerów, za pomocą reguł, które określają aplikacje, które mogą uzyskiwać dostęp do. Użytkowników z firm partnerskich można uzyskać dostęp do zasobów, kiedy ich potrzebują. Mogą one to robić bezpieczne, bez konieczności organizacji zapraszającej ręcznie dołączyć. Możesz z łatwością wdrożyć projekt w wybranej subskrypcji platformy Azure.

## <a name="as-is-code"></a>Jako — kod

Ten kod jest udostępniany jako przykład, aby zademonstrować użycie zaproszenia do interfejsu API usługi Azure Active Directory B2B. Powinien zostać dostosowany przez Twój zespół deweloperów lub partnerem i powinna zostać przejrzana pod przed ich wdrożeniem w środowisku produkcyjnym.

## <a name="next-steps"></a>Kolejne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Licencjonowanie współpracy platformy Azure w usłudze AD B2B](licensing-guidance.md)
* [Często zadawane pytania (FAQ) współpracy w usłudze Azure Active Directory B2B](faq.md)