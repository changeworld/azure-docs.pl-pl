---
title: Opis usługi Azure AD Connect 1.4.xx.x i zniknięcia urządzenia | Dokumenty firmy Microsoft
description: W tym dokumencie opisano problem wynikający z wersji 1.4.xx.x usługi Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176027"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Opis usługi Azure AD Connect 1.4.xx.x i zniknięcia urządzenia
W wersji 1.4.xx.x usługi Azure AD Connect niektórzy klienci mogą zobaczyć niektóre lub wszystkie ich urządzenia z systemem Windows znikają z usługi Azure AD. Nie jest to powód do niepokoju, ponieważ te tożsamości urządzeń nie są używane przez usługę Azure AD podczas autoryzacji dostępu warunkowego. Ta zmiana nie spowoduje usunięcia żadnych urządzeń z systemem Windows, które zostały poprawnie zarejestrowane w usłudze Azure AD dla hybrydowego sprzężenia usługi Azure AD.

Jeśli zostanie wyświetlone usunięcie obiektów urządzenia w usłudze Azure AD przekraczających próg usuwania eksportu, zaleca się, aby klient zezwolił na usunięcie. [Jak: zezwalaj na przepływ usuwania po przekroczeniu progu usunięcia](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Tło
Urządzenia z systemem Windows zarejestrowane jako przyłączone do usługi Azure Azure są reprezentowane w usłudze Azure AD jako obiekty urządzenia. Te obiekty urządzenia mogą być używane dla dostępu warunkowego. Urządzenia z systemem Windows 10 są synchronizowane z chmurą za pośrednictwem usługi Azure AD Connect, urządzenia z systemem Windows na poziomie down są rejestrowane bezpośrednio przy użyciu usług AD FS lub bezproblemowego logowania jednokrotnego.

## <a name="windows-10-devices"></a>Urządzenia z systemem Windows 10
Tylko urządzenia z systemem Windows 10 z określoną wartością atrybutu userCertificate skonfigurowaną przez hybrydowe przyłącze usługi Azure AD ma być synchronizowane z chmurą przez usługę Azure AD Connect. W poprzednich wersjach usługi Azure AD Connect to wymaganie nie było rygorystycznie wymuszane, co spowodowało niepotrzebne obiekty urządzenia w usłudze Azure AD. Takie urządzenia w usłudze Azure AD zawsze pozostawały w stanie "oczekujących", ponieważ te urządzenia nie były przeznaczone do rejestracji w usłudze Azure AD. 

Ta wersja usługi Azure AD Connect synchronizuje tylko urządzenia z systemem Windows 10, które są poprawnie skonfigurowane jako połączone z usługą Hybrid Azure AD. Obiekty urządzeń systemu Windows 10 bez przyłącza azure AD określonego userCertificate zostaną usunięte z usługi Azure AD.

## <a name="down-level-windows-devices"></a>Urządzenia z systemem Windows na poziomie down-level
Usługa Azure AD Connect nigdy nie powinna synchronizować [urządzeń z systemem Windows na poziomie down-level.](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices) Wszystkie urządzenia w usłudze Azure AD wcześniej niepoprawnie zsynchronizowane zostaną usunięte z usługi Azure AD. Jeśli usługa Azure AD Connect próbuje usunąć [urządzenia z systemem Windows w dół,](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)urządzenie nie jest urządzeniem utworzonym przez microsoft workplace join dla [komputerów z systemem MSI z systemem innym niż Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) i nie może być używane przez inną funkcję usługi Azure AD.

Niektórzy klienci mogą potrzebować ponownego odwiedzenia [narzędzia Jak: Zaplanuj hybrydowe sprzężenie usługi Azure Active Directory,](../devices/hybrid-azuread-join-plan.md) aby poprawnie zarejestrować swoje urządzenia z systemem Windows i upewnić się, że takie urządzenia mogą w pełni uczestniczyć w programie Dostęp warunkowy oparty na urządzeniu. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Jak sprawdzić, które urządzenia są usuwane za pomocą tej aktualizacji?

Aby sprawdzić, które urządzenia są usuwane, można użyć tego skryptu programu PowerShell:https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Ten skrypt generuje raport o certyfikatach przechowywanych w obiektach komputera usługi Active Directory, w szczególności certyfikatów wystawionych przez funkcję sprzężenia hybrydowego usługi Azure AD.
Sprawdza certyfikaty obecne we właściwości UserCertificate obiektu Computer w usłudze AD i sprawdza poprawność, czy certyfikat został wystawiony dla funkcji sprzężenia usługi Hybrid Azure AD (tj. Nazwa podmiotu jest zgodna z CN={ObjectGUID}).
Wcześniej usługa Azure AD Connect synchronizowała się z usługą Azure AD na dowolnym komputerze, który zawierał co najmniej jeden prawidłowy certyfikat, ale rozpoczynający się w usłudze Azure AD Connect w wersji 1.4, aparat synchronizacji może identyfikować certyfikaty hybrydowego dołączania usługi Azure AD i będzie "filtrował chmurę" komputer z synchronizacji do usługi Azure AD, chyba że istnieje prawidłowy certyfikat sprzężenia usługi Hybrid Azure AD.
Urządzenia usługi Azure AD, które zostały już zsynchronizowane z usługą AD, ale nie mają prawidłowego certyfikatu sprzężenia usługi Hybrid Azure AD, zostaną usunięte (CloudFiltered=TRUE) przez aparat synchronizacji.

## <a name="next-steps"></a>Następne kroki
- [Historia wersji usługi Azure AD Connect](reference-connect-version-history.md)
