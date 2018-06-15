---
title: Zapisywania się do portalu usługi Azure Active Directory B2B współpracy | Dokumentacja firmy Microsoft
description: Współpraca B2B usługi Azure Active Directory wspiera relacje między firmami, umożliwiając partnerom biznesowym selektywne uzyskiwanie dostępu do Twoich aplikacji firmowych
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/08/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5ee55034e84fe09484a2f7613cc2224be70fdebb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260162"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portal samoobsługowy do zapisywania do współpracy B2B usługi Azure AD

Klienci umożliwiają wykonywanie wielu z wbudowanych funkcji, które są dostępne za pośrednictwem [portalu Azure](https://portal.azure.com) i [panelu dostępu aplikacji](https://myapps.microsoft.com) dla użytkowników końcowych. Jednak może być konieczne dostosowanie przepływ pracy dołączania użytkowników B2B do potrzeb organizacji. Można to zrobić z [zaproszenia interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Jako organizacja zaproszenia może nie wiedzieć wcześniej, którzy są poszczególne zewnętrznych współpracowników którzy potrzebują dostępu do zasobów. Trzeba wprowadzić użytkowników z firm partnerskich o zarejestrowanie się przy użyciu zestawu zasad, które kontroluje jako zaproszenia organizacji. Ten scenariusz jest możliwe za pośrednictwem interfejsów API. Brak [przykładowy projekt w witrynie GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) wykonuje właśnie tę.

Ten projekt GitHub pokazuje, jak organizacje mogą używają interfejsów API zapewnienie opartych na zasadach, Samoobsługowe możliwość zapisywania do zaufanych partnerów, z zasadami, które określają aplikacje, które mogą uzyskiwać dostęp do. Użytkowników z firm partnerskich można uzyskać dostęp do zasobów, gdy to konieczne. Mogą one to robić bezpiecznego, bez konieczności zaproszenia organizacji ręcznie dołączyć je. Możesz z łatwością wdrożyć projekt do subskrypcji platformy Azure wybranych przez użytkownika.

## <a name="as-is-code"></a>Jako — kod

Ten kod jest udostępniany jako próbka do zaprezentowania użycia zaproszenie usługi Azure Active Directory B2B interfejsu API. Powinny zostać dostosowane przez zespół deweloperów lub partnera i należy ją sprawdzić przed wdrożeniem w środowisku produkcyjnym.

## <a name="next-steps"></a>Kolejne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Azure licencjonowania współpracy B2B usługi AD](licensing-guidance.md)
* [Azure współpracy B2B usługi Active Directory — często zadawane pytania (FAQ)](faq.md)