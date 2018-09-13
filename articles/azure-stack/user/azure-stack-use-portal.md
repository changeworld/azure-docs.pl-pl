---
title: Za pomocą portalu usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uzyskać dostęp do portalu użytkowników w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: mabrigg
ms.reviewer: efemmano
ms.openlocfilehash: 7ccf746cfd9645b63c2810bff83cfb0c4fa55d65
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713745"
---
# <a name="use-the-azure-stack-portal"></a>Używaj portalu Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Można subskrybować oferty publicznej za pomocą portalu usługi Azure Stack i korzystania z usług, które zapewniają te oferty. Jeśli używano globalne w witrynie Azure portal już znasz sposób działania lokacji.

## <a name="access-the-portal"></a>Przejdź do portalu

Operator usługi Azure Stack (dostawcy usług lub administratorem w organizacji), zostanie wyświetlone powiadomienie, poprawny adres URL, aby uzyskać dostęp do portalu.

- Zintegrowany system, aby uzyskać adres URL zależności na region i nazwy domeny zewnętrznej przez operatora i będzie w formacie https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
- Jeśli używasz usługi Azure Stack Development Kit portalu adres jest https://portal.local.azurestack.external.

![Przechwytywanie ekranu portalu użytkowników usługi Azure Stack](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Dostosowywanie pulpitu nawigacyjnego

Pulpit nawigacyjny zawiera domyślny zestaw kafelków. Możesz wybrać **Edytuj pulpit nawigacyjny** zmodyfikować domyślny pulpit nawigacyjny, lub wybierz **nowy pulpit nawigacyjny** do tworzenia niestandardowego pulpitu nawigacyjnego. Pulpit nawigacyjny można łatwo dostosować, dodając lub usuwając kafelków. Na przykład aby dodać tabliczkę obliczeń, wybierz pozycję **+ Utwórz zasób**. Kliknij prawym przyciskiem myszy **obliczenia**, a następnie wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.

## <a name="create-subscription-and-browse-available-resources"></a>Tworzenie subskrypcji i przeglądanie dostępnych zasobów

Jeśli nie masz już subskrypcję, w pierwszej kolejności należy zrobić to subskrybowaniu oferty. Po tym można przeglądać dostępne zasoby. Do przeglądania i tworzenia zasobów, użyj dowolnej z następujących metod:

- Wybierz **Marketplace** Kafelek na pulpicie nawigacyjnym.
- Na **wszystkie zasoby** kafelka, wybierz opcję **tworzyć zasoby**.
- W okienku nawigacji po lewej stronie wybierz **+ Utwórz zasób**.

## <a name="learn-how-to-use-available-services"></a>Dowiedz się, jak używać dostępnych usług

Aby uzyskać wskazówki dotyczące sposobu korzystania z dostępnych usług, prawdopodobnie różne opcje dostępne dla Ciebie.

- Organizacja lub dostawcy usług mogą zawierać we własnej dokumentacji, która jest typowe w przypadku, jeśli oferują one możliwość wyboru usług lub aplikacji.
- Aplikacje innych firm ma własną dokumentację.
- Dla usług spójnych z platformą Azure zdecydowanie zalecamy przejrzenie dokumentacji usługi Azure Stack. Aby uzyskać dostęp do dokumentacji użytkownika usługa Azure Stack, wybierz ikonę Pomoc, a następnie wybierz **Pomoc i obsługa techniczna**.

    ![Pomoc i obsługa techniczna opcji w interfejsie użytkownika](media/azure-stack-use-portal/HelpAndSupport.png)

    W szczególności zalecamy, aby przejrzeć następujące artykuły, aby rozpocząć pracę:

    - [Zagadnienia dotyczące klucza: przy użyciu usług lub tworzenia aplikacji dla usługi Azure Stack](azure-stack-considerations.md)
    - W **korzystania z usług** sekcji dokumentacji, jest w artykule uwagi dla każdej usługi. Na stronie zagadnienia opisano różnice między usług oferowanych na platformie Azure i tej samej usługi, które są oferowane w usłudze Azure Stack. Aby uzyskać przykład, zobacz [zagadnienia dotyczące maszyn wirtualnych](azure-stack-vm-considerations.md). Mogą istnieć inne informacje w **korzystania z usług** sekcji, która jest unikatowa w usłudze Azure Stack.

      Dokumentacja usługi Azure można użyć jako informacje ogólne, usługi, ale należy pamiętać o tych różnic. Zrozumienie, że dokumentacja łączy **samouczki Szybki Start** Kafelek wskaż dokumentacji platformy Azure.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Jeśli potrzebujesz pomocy, skontaktuj się z dostawcą organizacji lub usługi w celu uzyskania pomocy.

Jeśli używasz usługi Azure Stack Development Kit [forum usługi Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) jest jedynym źródłem pomocy technicznej.

## <a name="next-steps"></a>Kolejne kroki

[Zagadnienia dotyczące klucza: przy użyciu usług lub tworzenia aplikacji dla usługi Azure Stack](azure-stack-considerations.md)
