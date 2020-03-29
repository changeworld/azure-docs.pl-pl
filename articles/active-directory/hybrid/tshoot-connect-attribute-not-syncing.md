---
title: Rozwiązywanie problemów z synchronizacją atrybutu w usłudze Azure AD Connect | Dokumenty firmy Microsoft
description: W tym temacie przedstawiono kroki dotyczące rozwiązywania problemów z synchronizacją atrybutów przy użyciu zadania rozwiązywania problemów.
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
ms.openlocfilehash: a639b14c9313179816f6376aa0c5642a645ea344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60455965"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Rozwiązywanie problemów z synchronizacją atrybutu w usłudze Azure AD Connect

## <a name="recommended-steps"></a>**Zalecane kroki**

Przed zbadaniem problemów z synchronizacją atrybutów zapoznajmy się z procesem synchronizacji **usługi Azure AD Connect:**

  ![Proces synchronizacji usługi Azure AD Connect](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Spacja łącznika, tabela w bazie danych.
* **MV:** Metaverse, tabela w bazie danych.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Kroki synchronizacji**

* Import z usługi AD: obiekty usługi Active Directory są wprowadzane do usługi AD CS.

* Import z usługi AAD: Obiekty usługi Azure Active Directory są wprowadzane do usługi AAD CS.

* Synchronizacja: **Reguły synchronizacji ruchu przychodzącego** i **reguły synchronizacji ruchu wychodzącego** są uruchamiane w kolejności od niższej do wyższej. Aby wyświetlić reguły synchronizacji, można przejść do **Edytora reguł synchronizacji** z aplikacji klasycznych. **Reguły synchronizacji przychodzącej** wprowadzają dane z CS do MV. **Reguły synchronizacji wychodzącej** przenoszą dane z MV do CS.

* Eksport do usługi AD: Po uruchomieniu synchronizacji obiekty są eksportowane z usługi AD CS do **usługi Active Directory**.

* Eksportowanie do usługi AAD: Po uruchomieniu synchronizacji obiekty są eksportowane z usługi AAD CS do **usługi Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Badanie krok po kroku**

* Rozpoczniemy nasze wyszukiwanie od **Metaverse** i przyjrzymy się mapowaniu atrybutów od źródła do celu.

* Uruchom **Menedżera usług synchronizacji** z aplikacji klasycznych, jak pokazano poniżej:

  ![Uruchom Menedżera usług synchronizacji](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* W **Menedżerze usług synchronizacji**wybierz **opcję Wyszukiwanie Metaverse**, wybierz **pozycję Zakres według typu obiektu**, zaznacz obiekt za pomocą atrybutu i kliknij przycisk **Wyszukaj.**

  ![Metaverse — wyszukiwanie](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Kliknij dwukrotnie obiekt znaleziony w wyszukiwaniu **Metaverse,** aby wyświetlić wszystkie jego atrybuty. Można kliknąć kartę **Łączniki,** aby sprawdzić odpowiedni obiekt we wszystkich **miejscach łącznika**.

  ![Łączniki obiektów Metaverse](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Kliknij dwukrotnie **łącznik usługi Active Directory,** aby wyświetlić atrybuty **Spacja łączników.** Kliknij przycisk **Podgląd,** w następnym oknie dialogowym kliknij przycisk **Generuj podgląd.**

  ![Atrybuty obszaru łącznika](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Teraz kliknij przepływ **atrybutów Importuj**, pokazuje przepływ atrybutów z **obszaru łącznika usługi Active Directory** do **Metaverse**. **Kolumna Reguła synchronizacji** pokazuje, która **reguła synchronizacji** przyczyniła się do tego atrybutu. **Kolumna Źródło danych** zawiera atrybuty z **obszaru łącznika**. **Kolumna Atrybut Metaverse** pokazuje atrybuty w **metaverse**. Możesz wyszukać atrybut, który nie jest synchronizowany w tym miejscu. Jeśli nie znajdziesz atrybutu w tym miejscu, to nie jest mapowane i trzeba utworzyć nową niestandardową **regułę synchronizacji** do mapowania atrybutu.

  ![Atrybuty obszaru łącznika](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Kliknij **przepływ atrybutów eksportu** w lewym okienku, aby wyświetlić przepływ atrybutów z **metaverse** z powrotem do **obszaru łącznika usługi Active Directory** przy użyciu **reguł synchronizacji wychodzącej**.

  ![Atrybuty obszaru łącznika](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Podobnie można wyświetlić obiekt **obszaru łącznika usługi Azure Active Directory** i można wygenerować **podgląd,** aby wyświetlić przepływ atrybutów z **Metaverse** do **obszaru łącznika** i odwrotnie, w ten sposób można zbadać, dlaczego atrybut nie jest synchronizowany.

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
