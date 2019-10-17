---
title: Typowe scenariusze związane z zarządzaniem prawami w usłudze Azure AD (wersja zapoznawcza) — Azure Active Directory
description: Zapoznaj się z ogólnymi krokami, które należy wykonać w przypadku typowych scenariuszy w Azure Active Directory Zarządzanie prawami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81382ebecdff6c7b146386b3ae2b0768a7c834bf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389065"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Typowe scenariusze zarządzania prawami w usłudze Azure AD (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Istnieje kilka sposobów konfigurowania zarządzania prawami dla organizacji. Jeśli jednak dopiero zaczynasz, warto zrozumieć typowe scenariusze dla administratorów, osób zatwierdzających i zleceniodawców.

## <a name="administrators"></a>Administratorzy

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Jestem nowym uprawnieniem do zarządzania prawami i chcę uzyskać pomoc dotyczącą rozpoczynania pracy

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | [Postępuj zgodnie z samouczkiem, aby utworzyć pierwszy pakiet dostępu](entitlement-management-access-package-first.md) | [@no__t — ikona portalu 1Azure](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Chcę zezwolić użytkownikom w moim katalogu na żądania dostępu do grup, aplikacji lub witryn programu SharePoint

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** [Utwórz nowy pakiet dostępu w wykazie](entitlement-management-access-package-create.md#start-new-access-package) | ![Tworzenie pakietu dostępu](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Dodaj role zasobów, aby uzyskać dostęp do pakietu](entitlement-management-access-package-resources.md#add-resource-roles)<ul><li>Grupy</li><li>Aplikacje</li><li>Witryny programu SharePoint</li></ul> | ![Dodawanie ról zasobów](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Dodaj zasady](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)<ul><li>Dla użytkowników w katalogu</li><li>Wymagaj zatwierdzenia</li><li>Ustawienia cyklu życia</li></ul> | ![Dodawanie zasad](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Chcę zezwolić użytkownikom z katalogu partnerów służbowych (w tym użytkowników, którzy nie są jeszcze w katalogu) na żądania dostępu do grup, aplikacji lub witryn programu SharePoint

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** [Utwórz nowy pakiet dostępu w wykazie](entitlement-management-access-package-create.md#start-new-access-package) | ![Tworzenie pakietu dostępu](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Dodaj role zasobów, aby uzyskać dostęp do pakietu](entitlement-management-access-package-resources.md#add-resource-roles) | ![Dodawanie ról zasobów](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Dodaj zasady dla użytkowników zewnętrznych](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)<ul><li>Dla użytkowników nieznajdujących się w katalogu</li><li>Wymagaj zatwierdzenia</li><li>Ustawienia cyklu życia</li></ul> | ![Dodawanie zasad dla użytkowników zewnętrznych](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [Wyślij link, aby zażądać pakietu dostępu do partnera biznesowego](entitlement-management-access-package-settings.md)<ul><li>Partner biznesowy może udostępniać link swoim użytkownikom</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Chcę zmienić grupy, aplikacje lub witryny programu SharePoint w pakiecie dostępu

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** Otwórz pakiet dostępu | ![Dodawanie ról zasobów](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Dodaj lub Usuń role zasobów](entitlement-management-access-package-resources.md#add-resource-roles) | ![Dodawanie ról zasobów](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Chcę zobaczyć, kto ma przypisanie do grup, aplikacji lub witryn programu SharePoint

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** Otwórz pakiet dostępu | ![Dodawanie ról zasobów](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Wyświetlanie przypisań](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)<ul><li>Wyświetl użytkowników, którzy mają dostęp do pakietu dostępu</li><li>Wyświetl dostęp użytkownika, który wygasł</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Chcę wyświetlić grupy, aplikacje lub witryny programu SharePoint, do których użytkownik ma dostęp

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | [Wyświetl raport dotyczący przypisań użytkowników](entitlement-management-reports.md)<ul><li>Wyświetl, gdy zażądam i kto je zatwierdził</li></ul> |  |

## <a name="approvers"></a>Osoby zatwierdzające

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Chcę zatwierdzać żądania dostępu do grup, aplikacji lub witryn programu SharePoint

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** [Otwórz żądanie w portalu dostępu](entitlement-management-request-approve.md#open-request) | [@no__t — ikona portalu dostępu 1My](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Zatwierdź żądanie dostępu](entitlement-management-request-approve.md#approve-or-deny-request) | ![Zatwierdź dostęp](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Osoby żądające

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Chcę wyświetlić grupy, aplikacje lub witryny programu SharePoint dostępne dla mnie i zażądać dostępu

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** [Zaloguj się do portalu My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [@no__t — ikona portalu dostępu 1My](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Znajdź pakiet dostępu |  |
> | **3.** [Żądaj dostępu](entitlement-management-request-access.md#request-an-access-package) | ![Żądanie dostępu](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Jestem użytkownikiem zewnętrznym i chcę zażądać dostępu do grup, aplikacji lub witryn programu SharePoint za pomocą linku bezpośredniego

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** [Znajdź otrzymany link pakietu dostępu](entitlement-management-access-package-settings.md) |  |
> | **2.** [Zaloguj się do portalu My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [@no__t — ikona portalu dostępu 1My](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Żądaj dostępu](entitlement-management-request-access.md#request-an-access-package) | ![Żądaj dostępu użytkownika zewnętrznego](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Chcę wyświetlić grupy, aplikacje lub witryny programu SharePoint, do których już mam dostęp

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** [Zaloguj się do portalu My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [@no__t — ikona portalu dostępu 1My](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Wyświetl aktywne pakiety dostępu |  |

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie pierwszego pakietu dostępu](entitlement-management-access-package-first.md)
- [Delegowanie i role](entitlement-management-delegate.md)
