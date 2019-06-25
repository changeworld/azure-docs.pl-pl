---
title: Połącz dane usługi Azure AD przez wartownika platformy Azure w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie z danych usługi Azure Active Directory przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: ce4a57b8c266fe474fc2e6dd8f811fc7440e7ac6
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190435"
---
# <a name="connect-data-from-azure-active-directory"></a>Połącz dane z usługi Azure Active Directory

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wartownik platformy Azure umożliwia zbieranie danych z [usługi Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) i przesłać go strumieniowo do usługi Azure przez wartownika. Możesz wybrać do strumienia [dzienniki logowania](../active-directory/reports-monitoring/concept-sign-ins.md) i [dzienniki inspekcji](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli chcesz wyeksportować dane logowania z usługi Active Directory, musi mieć licencję usługi Azure AD P1 lub P2.

- Użytkownik z globalnego administratora lub zabezpieczeń uprawnienia administratora dla dzierżawy ma być przesyłana strumieniowo dzienniki z.

- Aby można było wyświetlić stan połączenia, musi mieć uprawnienia dostępu do dzienników diagnostycznych usługi Azure AD. 


## <a name="connect-to-azure-ad"></a>Łączenie z usługą Azure AD

1. Na platformie Azure przez wartownika, wybierz **łączników danych** a następnie kliknij przycisk **usługi Azure Active Directory** kafelka.

2. Obok dzienniki mają być przesyłane strumieniowo do usługi Azure przez wartownika, kliknij **Connect**.

6. Aby użyć odpowiednich schematu w usłudze Log Analytics dla alertów usługi Azure AD, możesz wyszukać **SigninLogs** i **AuditLogs**.




## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób łączenia usługi Azure AD na platformie Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).
