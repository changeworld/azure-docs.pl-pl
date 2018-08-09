---
title: Konfigurowanie nazwanych lokalizacji w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak konfigurowanie nazwanych lokalizacji.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.component: protection
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 62a55672a4326df585fc84699dfd72424be362dc
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627495"
---
# <a name="configure-named-locations-in-azure-active-directory"></a>Konfigurowanie nazwanych lokalizacji w usłudze Azure Active Directory

Za pomocą nazwanych lokalizacji możesz oznaczyć zaufanych zakresów adresów IP w Twojej organizacji. Nazwane lokalizacje w kontekście korzysta z usługi Azure Active Directory:

- Wykrywanie [zdarzeń o podwyższonym ryzyku](reports-monitoring/concept-risk-events.md) do zmniejszenia liczby zgłaszanych fałszywych alarmów.  

- [Dostęp warunkowy na podstawie lokalizacji](conditional-access/location-condition.md).


W tym artykule wyjaśniono, jak można skonfigurować lokalizacje z nazwą w danym środowisku.


## <a name="entry-points"></a>Punkty wejścia

Są dostępne na stronie konfiguracji nazwanych lokalizacji w **zabezpieczeń** części strony usługi Azure Active Directory, klikając pozycję:

![Punkty wejścia](./media/active-directory-named-locations/34.png)

- **Dostęp warunkowy:**

    - W **Zarządzaj** kliknij **lokalizacje z nazwą**.
    
        ![Polecenie lokalizacje nazwane](./media/active-directory-named-locations/06.png)

- **Ryzykowne logowania:**

    - Na pasku narzędzi u góry kliknij **Dodaj znane zakresów adresów IP**.

       ![Polecenie lokalizacje nazwane](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>Przykład konfiguracji

**Aby skonfigurować lokalizację o nazwie:**

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny.

2. W okienku po lewej stronie kliknij **usługi Azure Active Directory**.

    ![W okienku po lewej stronie łącze usługi Azure Active Directory](./media/active-directory-named-locations/01.png)

3. Na **usługi Azure Active Directory** stronie **zabezpieczeń** kliknij **dostępu warunkowego**.

    ![Polecenie dostępu warunkowego](./media/active-directory-named-locations/05.png)


4. Na **dostępu warunkowego** stronie **Zarządzaj** kliknij **lokalizacje z nazwą**.

    ![Polecenie lokalizacje nazwane](./media/active-directory-named-locations/06.png)


5. Na **lokalizacje z nazwą** kliknij **nową lokalizację**.

    ![Nowe polecenie lokalizacji](./media/active-directory-named-locations/07.png)


6. Na **New** wykonaj następujące czynności:

    ![Nowy blok](./media/active-directory-named-locations/61.png)

    a. W **nazwa** wpisz nazwę dla nazwanych lokalizacji.

    b. W **zakresów adresów IP** wpisz zakres adresów IP. Zakres adresów IP musi być zapisana w *Bezklasowego routingu międzydomenowego* formacie (CIDR).  

    c. Kliknij pozycję **Utwórz**.



## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz:

- [Dostęp warunkowy w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- [Warunki lokalizacji w funkcji dostępu warunkowego usługi Azure Active Directory](conditional-access/location-condition.md)

- [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](reports-monitoring/concept-risk-events.md).

- [Raport dotyczący ryzykownych logowań w portalu usługi Azure Active Directory](reports-monitoring/concept-risky-sign-ins.md).  
