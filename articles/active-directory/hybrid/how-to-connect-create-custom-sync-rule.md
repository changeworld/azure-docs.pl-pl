---
title: Jak dostosować regułę synchronizacji w usłudze Azure AD Connect | Dokumenty firmy Microsoft
description: W tym temacie przedstawiono kroki dotyczące rozwiązywania problemów z instalowaniem usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65d4c477d0e3aa9d5feea53e3e667ece651c83f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60351072"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Jak dostosować regułę synchronizacji

## <a name="recommended-steps"></a>**Zalecane kroki**

Za pomocą edytora reguł synchronizacji można edytować lub utworzyć nową regułę synchronizacji. Musisz być zaawansowanym użytkownikiem, aby wprowadzić zmiany w regułach synchronizacji. Wszelkie nieprawidłowe zmiany mogą spowodować usunięcie obiektów z katalogu docelowego. Przeczytaj [polecane dokumenty,](#recommended-documents) aby uzyskać wiedzę na temat reguł synchronizacji. Aby zmodyfikować regułę synchronizacji, przejdź do następujących kroków:

* Uruchom edytor synchronizacji z menu aplikacji na pulpicie, jak pokazano poniżej:

    ![Menu edytora reguł synchronizacji](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Aby dostosować domyślną regułę synchronizacji, sklonuj istniejącą regułę, klikając przycisk "Edytuj" w Edytorze reguł synchronizacji, który utworzy kopię standardowej reguły domyślnej i wyłącz ją. Zapisz sklonowaną regułę z pierwszeństwem mniejszym niż 100.  Pierwszeństwo określa, która reguła wygrywa (niższa wartość liczbowa) rozwiązywania konfliktów, jeśli istnieje konflikt przepływu atrybutów.

    ![Edytor reguł synchronizacji](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Podczas modyfikowania określonego atrybutu, najlepiej zachować tylko atrybut modyfikujący w sklonowanej regule.  Następnie włącz regułę domyślną, aby zmodyfikowany atrybut pochodził z sklonowanej reguły, a inne atrybuty były pobierane z domyślnej reguły standardowej. 

* Należy pamiętać, że w przypadku, gdy obliczona wartość zmodyfikowanego atrybutu jest NULL w sklonowanej regule i nie jest null w domyślnej reguły standardowej, a następnie wartość nie NULL wygra i zastąpi wartość NULL. Jeśli nie chcesz, aby wartość NULL została zastąpiona wartością nie NULL, przypisz autorytatywnyulc w sklonowanej regule.

* Aby zmodyfikować regułę **ruchu wychodzącego,** zmień filtr z edytora reguł synchronizacji.

## <a name="recommended-documents"></a>**Zalecane dokumenty**
* [Synchronizacja programu Azure AD Connect: zagadnienia techniczne](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Synchronizacja usługi Azure AD Connect: opis architektury](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Synchronizacja usługi Azure AD Connect: opis aprowizacji deklaratywnej](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Synchronizacja usługi Azure AD Connect: opis wyrażeń deklaratywnego inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Synchronizacja programu Azure AD Connect: opis konfiguracji domyślnej](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Synchronizacja usługi Azure AD Connect: opis użytkowników, grup i kontaktów](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Synchronizacja usługi Azure AD Connect: atrybuty w tle](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Następne kroki
- [Synchronizacja usługi Azure AD Connect](how-to-connect-sync-whatis.md).
- [Co to jest tożsamość hybrydowa?](whatis-hybrid-identity.md).
