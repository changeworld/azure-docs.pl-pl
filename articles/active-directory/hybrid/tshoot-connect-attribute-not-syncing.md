---
title: Rozwiązywanie problemów z atrybutu nie można zsynchronizować w programie Azure AD Connect | Dokumentacja firmy Microsoft
description: Ten temat zawiera procedurę rozwiązywania problemów z synchronizacją atrybutów za pomocą zadania rozwiązywania problemów.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60455965"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Rozwiązywanie problemów z atrybutu nie można zsynchronizować w programie Azure AD Connect

## <a name="recommended-steps"></a>**Zalecane czynności**

Przed sprawdzeniem atrybut synchronizowanie problemy, Przyjrzyjmy się **program Azure AD Connect** Synchronizowanie procesu:

  ![Proces synchronizacji programu Azure AD Connect](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Obszar łączników tabeli w bazie danych.
* **MV:** Metaverse, tabeli w bazie danych.
* **USŁUGI AD:** Usługa Active Directory
* **USŁUGI AAD:** Usługa Azure Active Directory

### <a name="synchronization-steps"></a>**Kroki synchronizacji**

* Importuj z usługi AD: Obiekty usługi Active Directory są włączane do usług AD CS.

* Importuj z usługi AAD: Obiekty usługi Active Directory systemu Azure są przenoszone do usługi AAD CS.

* Synchronizacja: **Reguły synchronizacji ruchu przychodzącego** i **reguły synchronizacji ruchu wychodzącego** są uruchamiane według numerze pierwszeństwa od niższych do wyższych. Aby wyświetlić reguły synchronizacji, możesz przejść do **Synchronization Rules Editor** z aplikacji klasycznych. **Reguły synchronizacji ruchu przychodzącego** zapewnia dodatkowe dane z CS MV. **Reguły synchronizacji ruchu wychodzącego** przenosi dane z MV do CS.

* Eksportowanie do usługi AD: Po uruchomieniu synchronizacji, obiekty są eksportowane z usług AD CS **usługi Active Directory**.

* Eksportowanie do usługi AAD: Po uruchomieniu synchronizacji, obiekty są eksportowane z usługi AAD CS **usługi Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Badanie krok po kroku**

* Firma Microsoft rozpocznie się nasze wyszukiwanie z **Metaverse** i przyjrzyj się mapowanie atrybutu ze źródła do docelowego.

* Uruchom **Menedżera usługi synchronizacji** z aplikacji pulpitu, jak pokazano poniżej:

  ![Uruchom Menedżera usługi synchronizacji](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* Na **Menedżera usługi synchronizacji**, wybierz opcję **wyszukiwanie magazynu Metaverse**, wybierz opcję **zakres według typu obiektu**, wybierz obiekt, za pomocą atrybutu, a kliknij **Wyszukiwania** przycisku.

  ![Metaverse Search](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Kliknij dwukrotnie obiekt w **Metaverse** Wyszukaj, aby wyświetlić jego atrybuty. Możesz kliknąć **łączników** kartę, aby przyjrzeć się odpowiedni obiekt we wszystkich **łączników**.

  ![Łączniki obiektu Metaverse](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Kliknij dwukrotnie **łącznika usługi Active Directory** do wyświetlania **obszaru łącznika** atrybutów. Kliknij pozycję **(wersja zapoznawcza)** przycisku, po kliknięciu następujące okno dialogowe na **Generowanie podglądu** przycisku.

  ![Atrybuty przestrzeni łącznika](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Teraz kliknąć **przepływu atrybutu importowania**, to przedstawia przepływ atrybutów z **obszaru łącznika usługi Active Directory** do **Metaverse**. **Reguła synchronizacji** pokazuje kolumny, która **reguły synchronizacji** przyczyniły się do tego atrybutu. **Źródło danych** kolumna zawiera atrybuty z **obszaru łącznika**. **Atrybut metaverse** kolumna zawiera atrybuty w **Metaverse**. Ten atrybut nie synchronizuje się w tym miejscu można wyszukać. Jeśli nie znajdziesz ten atrybut, w tym miejscu to to nie jest zamapowany i należy utworzyć nowy niestandardowy **reguły synchronizacji** do atrybutu mapowania.

  ![Atrybuty przestrzeni łącznika](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Kliknij pozycję **eksportowania przepływu atrybutu** w okienku po lewej stronie, aby wyświetlić przepływ atrybutów z **Metaverse** do **obszaru łącznika usługi Active Directory** przy użyciu  **Reguły synchronizacji ruchu wychodzącego**.

  ![Atrybuty przestrzeni łącznika](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Podobnie, można wyświetlić **obszaru łącznika usługi Active Directory Azure** obiektu i może generować **Podgląd** Aby wyświetlić przepływ atrybutów z **Metaverse** do **Obszaru łącznika** i na odwrót, w ten sposób możesz zbadać, dlaczego atrybut nie jest synchronizowany.

## <a name="recommended-documents"></a>**Zalecane dokumenty**
* [Synchronizacja w programie Azure AD Connect: zagadnienia techniczne](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Synchronizacja w programie Azure AD Connect: opis architektury](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Synchronizacja w programie Azure AD Connect: omówienie aprowizacji deklaratywnej](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Synchronizacja w programie Azure AD Connect: opis wyrażeń związanych z aprowizacją deklaratywną](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Synchronizacja w programie Azure AD Connect: opis konfiguracji domyślnej](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Synchronizacja w programie Azure AD Connect: opis użytkowników, grup i kontaktów](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Synchronizacja w programie Azure AD Connect: atrybuty w tle](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Następne kroki

- [Synchronizacja programu Azure AD Connect](how-to-connect-sync-whatis.md).
- [Co to jest tożsamość hybrydowa? ](whatis-hybrid-identity.md).
