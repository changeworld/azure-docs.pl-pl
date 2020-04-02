---
title: Przewodnik konfiguracji kont laboratorium przyspieszonego dla usług Azure Lab Services
description: Ten przewodnik pomaga administratorom szybko skonfigurować konto laboratoryjne do użytku w szkole.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: 88b37ea4ff717689f05afbb41d33a56a8cbb2c22
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547620"
---
# <a name="lab-account-setup-guide"></a>Przewodnik konfiguracji konta laboratorium

Pierwszym krokiem, który musi wykonać administratorzy, jest skonfigurowanie konta laboratorium w ramach subskrypcji platformy Azure.  Konto laboratorium jest kontenerem dla laboratoriów w klasie i zajmuje tylko kilka minut, aby skonfigurować.

## <a name="understand-your-schools-lab-account-requirements"></a>Poznaj wymagania dotyczące konta w laboratorium twojej szkoły

Aby zrozumieć, jak skonfigurować konto w laboratorium na podstawie potrzeb szkoły, należy rozważyć następujące pytania:

**Czy mam dostęp do subskrypcji platformy Azure?**

Aby utworzyć konto laboratorium, trzeba będzie mieć dostęp do subskrypcji platformy Azure, która jest skonfigurowana dla twojej szkoły; twoja szkoła może mieć jedną lub więcej subskrypcji.  Subskrypcja służy do zarządzania rozliczeniami i zabezpieczeniami dla wszystkich zasobów platformy Azure\usług, które są w niej używane, w tym kont laboratoryjnych.

**Ile kont laboratoryjnych musi zostać utworzonych?**

Aby szybko rozpocząć, rozsądne podejście jest utworzenie jednego konta laboratorium, a następnie później utworzyć dodatkowe konta laboratorium w razie potrzeby.  Na przykład może ostatecznie ewoluować do posiadania jednego konta laboratorium na dział.

**Kto powinien być właścicielami i współautorami konta laboratorium?**

Administratorzy są zazwyczaj właścicielami\współautorami konta laboratorium, ponieważ są odpowiedzialni za zarządzanie zasadami, które mają zastosowanie do wszystkich laboratoriów znajdujących się na koncie laboratorium.  Osoba, która tworzy konto laboratorium jest automatycznie właścicielem.  Można dodać dodatkowych właścicieli\współautorów (zazwyczaj z dzierżawy usługi AAD skojarzonej z subskrypcją), aby ułatwić zarządzanie kontem laboratorium, przypisując rolę Właściciel\Współautor na poziomie konta laboratorium.

**Kto będzie mógł tworzyć laboratoria\zarządzanie?**

Możesz zdecydować, że administratorzy i\lub wykładowcy będą tworzyć laboratoria i zarządzać nimi; ci użytkownicy (zazwyczaj z dzierżawy usługi AAD skojarzonej z subskrypcją) są przypisywani do roli Kreatora laboratorium na koncie laboratorium.

**Czy chcesz dać twórcom laboratorium możliwość zapisywania obrazów, które mogą być udostępniane w laboratoriach?**

Galeria obrazów udostępnionych to repozytorium, którego można używać do zapisywania i udostępniania obrazów.  Zaletą tego jest to, że jeśli masz kilka klas, które potrzebują tych samych obrazów, twórcy laboratorium można utworzyć obraz raz i udostępnić go w laboratoriach.  Jednak, aby rozpocząć, jest całkowicie uzasadnione, aby rozpocząć bez Shared Image Gallery; i zawsze możesz dodać go później.

Jeśli na to pytanie odpowiedziałeś "Tak", musisz utworzyć galerię obrazów udostępnionych do konta w laboratorium.  Jeśli odpowiedziałeś: "Nie wiem", możesz odroczyć tę decyzję na później.

Jeśli do konta laboratorium jest dołączona galeria obrazów udostępnionych

**Jakich obrazów w portalu Azure Marketplace będą używane laboratoria w klasie?**

Portal Azure Marketplace udostępnia setki obrazów, które można włączyć, dzięki czemu twórcy laboratorium mogą używać obrazu do tworzenia laboratorium.  Niektóre obrazy mogą zawierać wszystko, czego laboratorium już potrzebuje.  W innych przypadkach możesz użyć obrazu jako punktu wyjścia, a następnie twórca laboratorium może go dostosować, instalując dodatkowe aplikacje, narzędzia itp.

Jeśli nie wiesz, których obrazów będziesz musiał użyć, zawsze możesz wrócić do tego później, aby je włączyć.  Ponadto najlepszym sposobem, aby zobaczyć, które obrazy są dostępne jest najpierw utworzyć konto laboratorium - to daje dostęp, dzięki czemu można przejrzeć listę dostępnych obrazów i ich zawartość.  Więcej informacji znajduje się poniżej.
  
**Czy maszyny wirtualne laboratorium muszą mieć dostęp do innych zasobów platformy Azure lub zasobów prem?**

Podczas konfigurowania konta laboratorium, masz również możliwość równorzędnego z siecią wirtualną (VNet).  Aby zdecydować, czy trzeba równorzędnie z siecią wirtualną, należy wziąć pod uwagę następujące pytania:

- **Czy musisz zapewnić dostęp do serwera licencjonowania?**
  
   Jeśli planujesz używać obrazów portalu Azure Marketplace, koszt licencji systemu operacyjnego jest powiązany z cennikiem usług lab, więc *nie* musisz udzielać licencji dla samego systemu operacyjnego.  Jednak w przypadku dodatkowego oprogramowania\aplikacje, które są zainstalowane, należy podać licencję, zgodnie z potrzebami.

- **Czy maszyny wirtualne laboratorium potrzebują dostępu do innych zasobów na prem, takich jak udział plików, bazy danych itp.?**

   Sieć wirtualna musi zostać utworzona w celu zapewnienia dostępu do zasobów prem, zazwyczaj przy użyciu bramy sieci wirtualnej lokacji.  Jeśli nie masz skonfigurowane sieci wirtualnej, dodatkowy czas trzeba będzie zainwestować w tym celu.  Więcej informacji na temat konfigurowania tej opcji znajduje się poniżej.

- **Czy maszyny wirtualne laboratorium potrzebują dostępu do innych zasobów platformy Azure, które znajdują się w sieci wirtualnej?**

    Jeśli potrzebujesz dostępu do zasobów platformy Azure, które *nie* są zabezpieczone w sieci wirtualnej, a następnie można uzyskać dostęp do tych zasobów za pośrednictwem publicznego Internetu bez wykonywania żadnych komunikacji równorzędnej.

    Jeśli odpowiedziałeś "Tak" na jedno lub więcej pytań, musisz porównać konto laboratorium z siecią wirtualną.  Jeśli odpowiedziałeś: "Nie wiem", możesz odroczyć tę decyzję do później, ponieważ zawsze możesz wybrać opcję równorzędnego sieci wirtualnej po utworzeniu konta laboratorium.

## <a name="set-up-your-lab-account"></a>Konfigurowanie konta laboratoryjego

Po zapoznaniu się z wymaganiami dotyczącymi konta w laboratorium, możesz go skonfigurować.  Skorzystaj z linków w tej sekcji, aby dowiedzieć się, jak skonfigurować konto w laboratorium:

1. **Utwórz konto w laboratorium**

   Zapoznaj się z samouczka na [temat tworzenia konta laboratorium,](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) aby uzyskać instrukcje.

   Podczas tworzenia konta laboratorium, może okazać się przydatne do zapoznania się z zasobów platformy Azure zaangażowanych; aby uzyskać więcej informacji i wskazówek dotyczących tworzenia tych zasobów, można znaleźć na poniższej liście:

   - [Subskrypcja](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Grupa zasobów](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Konto laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Laboratorium w klasie](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Wybieranie regionu\Lokalizacja](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Wskazówki dotyczące nazewnictwa zasobów](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Dodawanie użytkowników do roli Twórca laboratorium**

   Zapoznaj się z samouczkiem na [temat dodawania użytkowników do roli Twórca laboratorium, aby](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role) uzyskać instrukcje.

   Ponadto, aby uzyskać więcej informacji na temat różnych ról, które mogą być przypisane do użytkowników, którzy będą zarządzać kontami laboratorium laboratorium i laboratoriów, zobacz [przewodnik na temat zarządzania tożsamością](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Łączenie się z siecią wirtualną równorzędną**

   Instrukcje można znaleźć w przewodniku instrukcjowym [dotyczących łączenia sieci laboratorium z siecią wirtualną równorzędną.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)

   Może być również konieczne zapoznanie się z instrukcjami [dotyczącymi konfigurowania zakresu adresów maszyn wirtualnych w laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Włączanie i przeglądanie obrazów**

    Zapoznaj się z instrukcjami dotyczącymi [włączania obrazów w Marketplace dla twórców laboratoriów,](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images) aby uzyskać instrukcje.

    Aby przejrzeć zawartość każdego obrazu w marketplace, kliknij nazwę obrazu.  Na przykład zapoznaj się z poniższym zrzutem ekranu, który pokazuje szczegóły obrazu maszyny wirtualnej nauki o danych Ubuntu:

    ![Przeglądanie obrazów w Marketplace](../media/setup-guide/review-marketplace-images.png)

    Jeśli masz dołączoną galerię obrazów udostępnionych do konta laboratorium i chcesz włączyć udostępnianie obrazów niestandardowych przez twórców laboratoriów, musisz wykonać podobne kroki, jak pokazano na poniższym zrzucie ekranu:

    ![Włączanie obrazów niestandardowych w Galerii obrazów udostępnionych](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły:

- [Zarządzanie kontami laboratorium](how-to-manage-lab-accounts.md)

- [Przewodnik po konfiguracji laboratorium w klasie](setup-guide.md)
