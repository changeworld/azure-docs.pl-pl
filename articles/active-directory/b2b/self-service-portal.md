---
title: Portal rejestracji samoobsługowej dla współpracy B2B — Usługa Azure AD
description: Współpraca B2B usługi Azure Active Directory wspiera relacje między firmami, umożliwiając partnerom biznesowym selektywne uzyskiwanie dostępu do Twoich aplikacji firmowych
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfa67f529408efcc2a703a4f80b15143c774f0b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77195797"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Samoobsługa dla rejestracji współpracy usługi Azure AD B2B

Klienci mogą wykonać wiele czynności dzięki wbudowanym funkcjom udostępnionym użytkownikom końcowym za pośrednictwem witryny [Azure Portal](https://portal.azure.com) oraz [panelu dostępu do aplikacji](https://myapps.microsoft.com). Może się jednak okazać, że w zależności od potrzeb organizacji konieczne będzie dostosowanie przepływu pracy dołączania dla użytkowników B2B.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Zarządzanie uprawnieniami usługi Azure AD dla rejestracji użytkownika gościa B2B

Jako organizacja zapraszająca możesz nie wiedzieć z wyprzedzeniem, kim są poszczególni zewnętrzni współpracownicy, którzy potrzebują dostępu do Twoich zasobów. Potrzebujesz możliwości, aby użytkownicy z firm partnerskich zarejestrowali się w zasadach, które kontrolujesz. Aby umożliwić użytkownikom z innych organizacji żądanie dostępu, a po zatwierdzeniu można aprowizować konta gości i przypisywać je do grup, aplikacji i witryn usługi SharePoint Online, można użyć [zarządzania uprawnieniami usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) do konfigurowania zasad [zarządzania dostępem dla użytkowników zewnętrznych.](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users)

## <a name="azure-active-directory-b2b-invitation-api"></a>Interfejs API zaproszenia usługi Azure Active Directory B2B

Organizacje mogą używać [interfejsu API menedżera zaproszeń programu Microsoft Graph](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) do tworzenia własnych środowisk dołączania dla użytkowników-gości B2B. Jeśli chcesz zaoferować samoobsługową rejestrację użytkownika-gościa B2B, zalecamy korzystanie z [zarządzania uprawnieniami usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Ale jeśli chcesz utworzyć własne środowisko, możesz użyć [interfejsu API tworzenia zaproszenia](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) do automatycznego wysyłania dostosowanej wiadomości e-mail z zaproszeniem bezpośrednio do użytkownika B2B, na przykład. Lub aplikacja może użyć inviteRedeemUrl zwrócone w odpowiedzi na tworzenie do tworzenia własne zaproszenie (za pośrednictwem mechanizmu komunikacji wyboru) do zaproszonego użytkownika.

## <a name="next-steps"></a>Następne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Licencjonowanie funkcji współpracy B2B w usłudze Azure AD](licensing-guidance.md)
* [Współpraca B2B w usłudze Active Directory Azure — często zadawane pytania](faq.md)
