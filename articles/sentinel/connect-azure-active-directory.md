---
title: Łączenie danych usługi Azure AD z usługą Azure Sentinel | Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane usługi Azure Active Directory z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: be9241a6156621d3f90dbab2da5bebeb463b4232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588624"
---
# <a name="connect-data-from-azure-active-directory"></a>Łączenie danych z usługi Azure Active Directory



Usługa Azure Sentinel umożliwia zbieranie danych z [usługi Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) i przesyłanie strumieniowe ich do usługi Azure Sentinel. Można wybrać opcję przesyłania strumieniowego [dzienników logowania](../active-directory/reports-monitoring/concept-sign-ins.md) i [dzienników inspekcji.](../active-directory/reports-monitoring/concept-audit-logs.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli chcesz wyeksportować dane logowania z usługi Active Directory, musisz mieć licencję usługi Azure AD P1 lub P2.

- Użytkownik z uprawnieniami administratora administracyjnego globalnego lub zabezpieczeń w dzierżawie, z której chcesz przesyłać strumieniowo dzienniki.

- Aby móc zobaczyć stan połączenia, musisz mieć uprawnienia dostępu do dzienników diagnostycznych usługi Azure AD. 


## <a name="connect-to-azure-ad"></a>Łączenie z usługą Azure AD

1. W usłudze Azure Sentinel wybierz **łączniki danych,** a następnie kliknij kafelek **usługi Azure Active Directory.**

1. Obok dzienników, które chcesz przesyłać strumieniowo do usługi Azure Sentinel, kliknij pozycję **Połącz**.

1. Można wybrać, czy alerty z usługi Azure AD mają automatycznie generować zdarzenia w usłudze Azure Sentinel. W obszarze **Tworzenie zdarzeń** wybierz **pozycję Włącz,** aby włączyć domyślną regułę analityczną, która automatycznie tworzy zdarzenia z alertów generowanych w połączonej usłudze zabezpieczeń. Następnie można edytować tę regułę w obszarze **Analytics,** a następnie **Aktywne reguły**.

1. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla alertów usługi Azure AD, wyszukaj **dzienniki logowania** i **dzienniki inspekcji.**




## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć usługę Azure AD z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
