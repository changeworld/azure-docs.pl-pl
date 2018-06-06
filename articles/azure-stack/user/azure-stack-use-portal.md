---
title: Przy użyciu portalu Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostępu i korzystanie z aplikacji portal użytkowników w stosie Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: mabrigg
ms.reviewer: efemmano
ms.openlocfilehash: 7ca29ee359349f69c3d5ff21bd9db3f93358206a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724316"
---
# <a name="use-the-azure-stack-portal"></a>Używaj portalu Azure Stack

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można korzystać z portalu Azure stosu do subskrybowania publiczne oferty i korzystać z usług, które zapewniają te oferty. Jeśli używano globalne portalu Azure, już znasz działania lokacji.

## <a name="access-the-portal"></a>Dostępu do portalu

Operatorem stosu Azure (dostawcy usług lub administrator w organizacji), może powiadamiać o poprawny adres URL do dostępu do portalu.

- Zintegrowany system, zmienia się na podstawie od regionu i nazwy domen zewnętrznych z operatorem adres URL, a musi być w formacie https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
- Jeśli używasz usługi Azure stosu Development Kit adres portalu jest https://portal.local.azurestack.external.

![Przechwytywanie ekranu portalu Azure stosu użytkownika](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Dostosowywać pulpit nawigacyjny

Pulpit nawigacyjny zawiera domyślny zestaw kafelków. Możesz wybrać **edycji pulpitu nawigacyjnego** do modyfikacji domyślnego pulpitu nawigacyjnego lub wybierz **nowego pulpitu nawigacyjnego** utworzenie niestandardowego pulpitu nawigacyjnego. Można łatwo dostosować pulpit nawigacyjny, dodając lub usuwając Kafelki. Na przykład, aby dodać pole obliczeniowe, zaznacz **nowy**. Kliknij prawym przyciskiem myszy **obliczeniowe**, a następnie wybierz **Przypnij do pulpitu nawigacyjnego**.

## <a name="create-subscription-and-browse-available-resources"></a>Tworzenie subskrypcji i przeglądanie dostępnych zasobów

Jeśli nie masz już subskrypcję, w pierwszej kolejności należy wykonać to subskrybować oferty. Następnie można przeglądać dostępne zasoby. Przeglądanie i utworzyć zasobów, użyj jednej z następujących metod:

- Wybierz **Marketplace** kafelka na pulpicie nawigacyjnym.
- Na **wszystkie zasoby** kafelka, wybierz opcję **Utwórz zasoby**.
- W okienku nawigacji po lewej stronie wybierz **nowy**.

## <a name="learn-how-to-use-available-services"></a>Dowiedz się, jak używać dostępnych usług

Aby uzyskać wskazówki dotyczące sposobu używania dostępnych usług, mogą istnieć różne opcje dostępne dla Ciebie.

- Organizacja lub dostawcy usług mogą zawierać własnej dokumentacji, która jest typowe w przypadku, jeśli oferują możliwość wyboru usług lub aplikacji.
- Aplikacje innych producentów ma własną dokumentację.
- Usług Azure spójne zaleca się przejrzenie dokumentacji stosu Azure. Aby uzyskać dostęp do dokumentacji użytkownika stosu Azure, wybierz ikonę Pomoc, a następnie wybierz **Pomoc i obsługa techniczna**.

    ![Pomoc i obsługa techniczna opcji w interfejsie użytkownika](media/azure-stack-use-portal/HelpAndSupport.png)

    W szczególności zalecamy zapoznanie się następujące artykuły, aby rozpocząć:

    - [Kluczowe zagadnienia dotyczące: za pomocą usług lub tworzenia aplikacji dla platformy Azure stosu](azure-stack-considerations.md)
    - W **korzystania z usług** sekcji dokumentacji, jest artykułu zagadnienia, dla każdej usługi. Na stronie zagadnienia opisano różnice między usług oferowanych na platformie Azure i tej samej usługi oferowane w stosie Azure. Na przykład zobacz [zagadnienia dotyczące maszyny Wirtualnej](azure-stack-vm-considerations.md). Mogą istnieć inne informacje w **korzystania z usług** sekcja, która jest unikatowa dla stosu Azure.

      Dokumentację platformy Azure można używać jako informacje ogólne, dla usługi, ale należy pamiętać o tych różnic. Zrozumienie, że dokumentację łączy **samouczków szybkiego startu** kafelka punktu dokumentacji platformy Azure.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Jeśli potrzebujesz pomocy technicznej, skontaktuj się z dostawcą organizacji lub usługi, aby uzyskać pomoc.

Jeśli używasz usługi Azure stosu Development Kit [forum Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) jest jedynym źródłem pomocy technicznej.

## <a name="next-steps"></a>Kolejne kroki

[Kluczowe zagadnienia dotyczące: za pomocą usług lub tworzenia aplikacji dla platformy Azure stosu](azure-stack-considerations.md)
