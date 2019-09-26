---
title: Zrozumienie Azure AD Connect 1.4. XX. x i znikanie urządzenia | Microsoft Docs
description: W tym dokumencie opisano problem związany z wersją 1.4. XX. x Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 1ba17feea880bb55c3b4a14a06b3d803dba2350a
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316956"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Zrozumienie Azure AD Connect 1.4. XX. x i znikanie urządzenia
W wersji 1.4. XX. x z Azure AD Connect niektórzy klienci mogą zobaczyć, że niektóre lub wszystkie urządzenia z systemem Windows znikną z usługi Azure AD. Nie jest to przyczyną problemu, ponieważ te tożsamości urządzeń nie są używane przez usługę Azure AD podczas autoryzacji dostępu warunkowego. Ta zmiana nie spowoduje usunięcia wszystkich urządzeń z systemem Windows, które zostały poprawnie zarejestrowane w usłudze Azure AD dla hybrydowego sprzężenia usługi Azure AD.

Jeśli widzisz usuwanie obiektów urządzeń w usłudze Azure AD przekraczających próg usuwania eksportu, zaleca się, aby klient zezwolił na operacje usuwania. [Instrukcje: Zezwalanie na usuwanie do przepływu po przekroczeniu progu usuwania](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Tło
Urządzenia z systemem Windows zarejestrowane jako dołączone do hybrydowej usługi Azure AD są reprezentowane w usłudze Azure AD jako obiekty urządzeń. Te obiekty urządzenia mogą być używane na potrzeby dostępu warunkowego. Urządzenia z systemem Windows 10 są synchronizowane z chmurą za pośrednictwem Azure AD Connect. urządzenia z systemem Windows niższego poziomu są rejestrowane bezpośrednio przy użyciu AD FS lub bezproblemowego logowania jednokrotnego.

## <a name="windows-10-devices"></a>Urządzenia z systemem Windows 10
Tylko urządzenia z systemem Windows 10 z określoną wartością atrybutu userCertificate skonfigurowanym przez sprzężenie hybrydowe usługi Azure AD powinny być synchronizowane z chmurą przez Azure AD Connect. W poprzednich wersjach Azure AD Connect ten wymóg nie został rygorystycznie wymuszony, co powoduje niepotrzebne obiekty urządzenia w usłudze Azure AD. Takie urządzenia w usłudze Azure AD zawsze pozostają w stanie "oczekiwanie", ponieważ te urządzenia nie zostały przeznaczone do rejestracji w usłudze Azure AD. 

Ta wersja Azure AD Connect będzie synchronizować tylko urządzenia z systemem Windows 10, które są poprawnie skonfigurowane do obsługi hybrydowej usługi Azure AD. Obiekty urządzeń z systemem Windows 10, które nie są określone przez usługę Azure AD Join userCertificate, zostaną usunięte z usługi Azure AD.

## <a name="down-level-windows-devices"></a>Urządzenia z systemem Windows niższego poziomu
Azure AD Connect nigdy nie należy synchronizować [urządzeń z systemem Windows niższego poziomu](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Wszystkie urządzenia w usłudze Azure AD, które wcześniej zostały zsynchronizowane, zostaną teraz usunięte z usługi Azure AD. Jeśli Azure AD Connect próbuje usunąć urządzenia z [systemem Windows niższego poziomu](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices), to urządzenie nie jest tym, które zostało utworzone przez [program Microsoft Workplace Join dla komputerów z systemem innym niż Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) , i nie może być używane przez żadną inną funkcję usługi Azure AD.

Niektórzy klienci mogą potrzebować odwiedzania [, jak: Zaplanuj implementację](../devices/hybrid-azuread-join-plan.md) hybrydowej funkcji dołączania Azure Active Directory, aby urządzenia z systemem Windows zostały prawidłowo zarejestrowane i zapewnić, że takie urządzenia mogą w pełni uczestniczyć w dostępie warunkowym opartym na urządzeniach. 

## <a name="next-steps"></a>Następne kroki
- [Historia wersji Azure AD Connect](reference-connect-version-history.md)
