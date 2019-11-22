---
title: Portal rejestracji samoobsługowej współpracy B2B — Azure AD
description: Współpraca B2B usługi Azure Active Directory wspiera relacje między firmami, umożliwiając partnerom biznesowym selektywne uzyskiwanie dostępu do Twoich aplikacji firmowych
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 794a13a3f863c732d4e7ed8cedcbd73f7cbc0d0b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272106"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portal samoobsługowy do rejestracji do funkcji współpracy B2B w usłudze Azure AD

Klienci mogą wykonać wiele czynności dzięki wbudowanym funkcjom udostępnionym użytkownikom końcowym za pośrednictwem witryny [Azure Portal](https://portal.azure.com) oraz [panelu dostępu do aplikacji](https://myapps.microsoft.com). Może się jednak okazać, że w zależności od potrzeb organizacji konieczne będzie dostosowanie przepływu pracy dołączania dla użytkowników B2B. Można to zrobić za pomocą [interfejsu API zaproszenia](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Reprezentując organizację zapraszającą, możesz nie wiedzieć z odpowiednim wyprzedzeniem, którzy konkretnie zewnętrzni współpracownicy potrzebują dostępu do Twoich zasobów. Potrzebujesz rozwiązania, w ramach którego użytkownicy z firm partnerskich będą rejestrować się w oparciu o zbiór zasad, które kontrolujesz w imieniu organizacji zapraszającej. Ten scenariusz jest możliwy dzięki interfejsom API. Istnieje [przykładowy projekt w serwisie GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web), który to umożliwia.

Ten projekt w serwisie GitHub pokazuje, jak organizacje mogą używać interfejsów API w celu udostępnienia zaufanym partnerom samoobsługowej funkcji rejestracji w oparciu o odpowiednie zasady wraz z regułami określającymi aplikacje, do których mogą oni uzyskać dostęp. Użytkownicy z firm partnerskich mogą uzyskać dostęp do zasobów, gdy ich potrzebują. Mogą to zrobić w sposób bezpieczny, bez konieczności ręcznego dodawania ich przez organizację zapraszającą. Ten projekt możesz z łatwością wdrożyć w wybranej subskrypcji platformy Azure.

## <a name="as-is-code"></a>Kod w stanie takim, w jakim jest

Ten kod jest udostępniany jako przykład, aby zademonstrować użycie interfejsu API zaproszenia do współpracy B2B w usłudze Azure Active Directory. Przed jego wdrożeniem w scenariuszu produkcyjnym powinien zostać dostosowany przez Twój zespół deweloperów lub partnera oraz sprawdzony.

## <a name="next-steps"></a>Następne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Licencjonowanie funkcji współpracy B2B w usłudze Azure AD](licensing-guidance.md)
* [Współpraca B2B w usłudze Active Directory Azure — często zadawane pytania](faq.md)
