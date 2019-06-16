---
title: Typowe scenariusze zarządzania uprawnień usługi Azure AD (wersja zapoznawcza) — usługi Azure Active Directory
description: Dowiedz się, ogólne kroki, które należy wykonać dla typowych scenariuszy w usłudze Azure Active Directory Zarządzanie uprawnieniami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a50f4a8a63022668dac68c974f8c828c72777c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66473134"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Typowe scenariusze w zarządzanie uprawnieniami w usłudze Azure AD (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie uprawnieniami w usłudze Azure Active Directory (Azure AD) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Istnieje kilka sposobów, że możesz skonfigurować zarządzanie uprawnieniami w Twojej organizacji. Jednak jeśli po prostu rozpoczynasz pracę, warto poznać typowe scenariusze dla administratorów, osoby zatwierdzające i dostępu komputerom zgłaszającym żądania.

## <a name="administrators"></a>Administratorzy

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Jestem nowym Zarządzanie uprawnieniami i chcę, aby uzyskać pomoc dotyczącą wprowadzenie

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | [Postępuj zgodnie z samouczkiem, aby utworzyć Twój pierwszy pakiet dostępu](entitlement-management-access-package-first.md) | [![Ikona usługi Azure portal](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Chcę umożliwić użytkownikom w katalogu, aby zażądać dostępu do grup, aplikacji lub witryny programu SharePoint

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** [Utwórz nowy pakiet dostępu w wykazie](entitlement-management-access-package-create.md#start-new-access-package) | ![Tworzenie pakietu dostępu](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Dodawanie ról zasobów w celu dostępu do pakietu](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>Grupy</li><li>Aplikacje</li><li>Witryny programu SharePoint</li></ul> | ![Dodawanie ról zasobów](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Dodawanie zasad](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>Dla użytkowników w katalogu</li><li>Wymagaj zatwierdzenia</li><li>Ustawienia wygasania</li></ul> | ![Dodawanie zasad](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Chcę umożliwić użytkownikom w katalogu partnerów biznesowych (w tym użytkownicy nie są jeszcze w moim katalogu), aby zażądać dostępu do grup, aplikacji lub witryny programu SharePoint

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** [Utwórz nowy pakiet dostępu w wykazie](entitlement-management-access-package-create.md#start-new-access-package) | ![Tworzenie pakietu dostępu](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Dodawanie ról zasobów w celu dostępu do pakietu](entitlement-management-access-package-edit.md#add-resource-roles) | ![Dodawanie ról zasobów](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Dodaj zasady dla użytkowników zewnętrznych](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>Dla użytkowników spoza katalogu</li><li>Wymagaj zatwierdzenia</li><li>Ustawienia wygasania</li></ul> | ![Dodaj zasady dla użytkowników zewnętrznych](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [Wyślij łącze portalu Moje dostępu do żądania pakietu dostęp partnera biznesowego](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>Partner biznesowy udostępnić link użytkownikom</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Zmienianie grup, aplikacji lub witryny programu SharePoint w pakiecie dostępu

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** Otwórz pakiet dostępu | ![Dodawanie ról zasobów](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Dodawanie lub usuwanie ról zasobów](entitlement-management-access-package-edit.md#add-resource-roles) | ![Dodawanie ról zasobów](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Czy chcesz wyświetlić, kto ma przypisania do grup, aplikacji lub witryny programu SharePoint

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** Otwórz pakiet w ramach dostępu | ![Dodawanie ról zasobów](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Wyświetl przypisania](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>Wyświetl użytkowników, którzy mają dostęp do pakietów programu access</li><li>Widok dostępu użytkownika, który utracił ważność.</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Chcę wyświetlić grupy, aplikacji lub witryny programu SharePoint, których użytkownik ma dostęp do

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | [Wyświetl raport przypisania użytkownika](entitlement-management-reports.md)<ul><li>Widok, gdy wymagane są i kto zatwierdzone</li></ul> |  |

## <a name="approvers"></a>Osoby zatwierdzające

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Czy chcesz zatwierdzić żądań dostępu do grup, aplikacji lub witryny programu SharePoint

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** [Otwórz zapytanie w portalu Moje dostępu](entitlement-management-request-approve.md#open-request) | [![Ikona portalu Moje dostępu](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Zatwierdź żądanie dostępu](entitlement-management-request-approve.md#approve-or-deny-request) | ![Zatwierdzić dostęp](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Obiekty żądające

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Chcę, aby wyświetlić grupy, aplikacje lub witryny programu SharePoint, które oferuje mi system i żądać dostępu

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** [Zaloguj się do portalu Moje dostępu](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Ikona portalu Moje dostępu](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Dostęp do pakietu |  |
> | **3.** [Poproś o dostęp](entitlement-management-request-access.md#request-an-access-package) | ![Żądaj dostępu](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Jestem użytkownikiem zewnętrznym i chcę, aby zawnioskować o dostęp do grup, aplikacji lub witryny programu SharePoint przy użyciu linku bezpośredniego

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** [Znajdź mój link dostępu do portalu otrzymany](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [Zaloguj się do portalu Moje dostępu](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Ikona portalu Moje dostępu](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Poproś o dostęp](entitlement-management-request-access.md#request-an-access-package) | ![Żądanie dostępu użytkowników zewnętrznych](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Chcę wyświetlić grupy, aplikacji lub witryny programu SharePoint, który już mam dostęp do

> [!div class="mx-tableFixed"]
> | Kroki | Przykład |
> | --- | --- |
> | **1.** [Zaloguj się do portalu Moje dostępu](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Ikona portalu Moje dostępu](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Wyświetl pakiety active dostępu |  |

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Utworzenie pierwszego pakietu dostępu](entitlement-management-access-package-first.md)
- [Edytowanie istniejącego pakietu dostępu i zarządzanie nim](entitlement-management-access-package-edit.md)
