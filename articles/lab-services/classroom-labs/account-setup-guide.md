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
ms.openlocfilehash: 8fcc46487e7f7c2d075639f10a30cae9950ff31b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879585"
---
# <a name="lab-account-setup-guide"></a>Przewodnik konfiguracji konta laboratorium

W pierwszym kroku administratorzy powinni skonfigurować konto laboratorium w ramach subskrypcji platformy Azure. Konto laboratorium jest kontenerem dla laboratoriów w klasie i zajmuje tylko kilka minut, aby skonfigurować.

## <a name="understand-your-schools-lab-account-requirements"></a>Poznaj wymagania dotyczące konta w laboratorium twojej szkoły

Aby zrozumieć, jak skonfigurować konto w laboratorium na podstawie potrzeb szkoły, należy rozważyć te pytania.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Czy mam dostęp do subskrypcji platformy Azure?

Aby utworzyć konto laboratorium, potrzebujesz dostępu do subskrypcji platformy Azure, która jest skonfigurowana dla twojej szkoły. Twoja szkoła może mieć jedną lub więcej subskrypcji. Subskrypcja służy do zarządzania rozliczeniami i zabezpieczeniami dla wszystkich zasobów i usług platformy Azure, w tym kont laboratoryjnych.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Ile kont laboratoryjnych musi zostać utworzonych?

Aby szybko rozpocząć, należy utworzyć jedno konto laboratorium, a następnie utworzyć dodatkowe konta w laboratorium w razie potrzeby. Na przykład może mieć po raz pierwszy jedno konto laboratorium na dział.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Kto powinien być właścicielami i współautorami konta laboratorium?

Administratorzy są zazwyczaj właścicielami i współautorami konta laboratorium. Są one odpowiedzialne za zarządzanie zasadami, które mają zastosowanie do wszystkich laboratoriów zawartych w koncie laboratorium. Osoba, która tworzy konto laboratorium jest automatycznie właścicielem. Można dodać dodatkowych właścicieli i współautorów, zazwyczaj z dzierżawy usługi Azure Active Directory (Azure AD) skojarzonych z subskrypcją. Może to być przydatne do zarządzania kontem laboratorium, przypisując rolę właściciela lub współautora na poziomie konta laboratorium.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Kto będzie mógł tworzyć laboratoria i zarządzać nimi?

Możesz wybrać, aby administratorzy i wykładowcy tworzyli laboratoria i zarządzali nimi. Ci użytkownicy (zazwyczaj z dzierżawy usługi Azure AD skojarzonej z subskrypcją) są przypisywani do roli Kreatora laboratorium na koncie laboratorium.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Czy chcesz dać twórcom laboratorium możliwość zapisywania obrazów, które mogą być udostępniane w laboratoriach?

Galeria obrazów udostępnionych to repozytorium, którego można używać do zapisywania i udostępniania obrazów. Jeśli masz kilka klas, które potrzebują tych samych obrazów, twórcy laboratorium mogą utworzyć obraz raz i udostępnić go w laboratoriach. Jednak, aby rozpocząć, nie musisz koniecznie galerii obrazów udostępnionych, ponieważ zawsze możesz dodać ją później.

Jeśli na to pytanie odpowiedziałeś "tak", musisz utworzyć lub dołączyć galerię zdjęć udostępnionych do konta w laboratorium. Jeśli odpowiedziałeś "Nie wiem", możesz odroczyć tę decyzję do później.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Jakich obrazów w portalu Azure Marketplace będą używane laboratoria w klasie?

Usługa Azure Marketplace udostępnia setki obrazów, które można włączyć, dzięki czemu twórcy laboratorium mogą używać obrazu do tworzenia laboratorium. Niektóre obrazy mogą zawierać wszystko, czego laboratorium już potrzebuje. W innych przypadkach można użyć obrazu jako punktu wyjścia, a następnie twórca laboratorium można dostosować go, instalując dodatkowe aplikacje lub narzędzia.

Jeśli nie wiesz, których obrazów będziesz musiał użyć, zawsze możesz wrócić do tego później, aby je włączyć. Ponadto najlepszym sposobem, aby zobaczyć, które obrazy są dostępne jest najpierw utworzyć konto laboratorium. Daje to dostęp, dzięki czemu można przejrzeć listę dostępnych obrazów i ich zawartość.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Czy maszyny wirtualne laboratorium muszą mieć dostęp do innych zasobów platformy Azure lub lokalnych?

Po skonfigurowaniu konta laboratorium, masz również możliwość równorzędnego z siecią wirtualną. Aby zdecydować, czy jest to potrzebne, rozważ następujące pytania:

- **Czy musisz zapewnić dostęp do serwera licencjonowania?**
  
   Jeśli planujesz używać obrazów portalu Azure Marketplace, koszt licencji systemu operacyjnego jest powiązany z cennikiem usług laboratoryjnych. W związku z tym nie trzeba udzielać licencji dla samego systemu operacyjnego. Jednak w przypadku dodatkowego oprogramowania i aplikacji, które są zainstalowane, należy podać licencję, stosownie do przypadku.

- **Czy maszyny wirtualne laboratorium potrzebują dostępu do innych zasobów lokalnych, takich jak udział plików lub baza danych?**

   Tworzenie sieci wirtualnej w celu zapewnienia dostępu do zasobów lokalnych, zazwyczaj przy użyciu bramy sieci wirtualnej lokacji lokacji. Jeśli nie masz skonfigurowaną sieci wirtualnej, musisz poświęcić na to dodatkowy czas.

- **Czy maszyny wirtualne laboratorium potrzebują dostępu do innych zasobów platformy Azure, które znajdują się w sieci wirtualnej?**

   Jeśli potrzebujesz dostępu do zasobów platformy Azure, które *nie* są zabezpieczone w sieci wirtualnej, możesz uzyskać dostęp do tych zasobów za pośrednictwem publicznego Internetu, bez wykonywania komunikacji równorzędnej.

Jeśli odpowiedziałeś "tak" na jedno lub więcej pytań, musisz porównać konto laboratorium z siecią wirtualną. Jeśli odpowiedziałeś "Nie wiem", możesz odroczyć tę decyzję do później. Zawsze można wybrać utworzenie sieci wirtualnej równorzędnej po utworzeniu konta laboratorium.

## <a name="set-up-your-lab-account"></a>Konfigurowanie konta laboratoryjego

Po zapoznaniu się z wymaganiami dotyczącymi konta w laboratorium możesz go skonfigurować.

1. **Utwórz konto w laboratorium.** Zapoznaj się z samouczka na [temat tworzenia konta laboratorium,](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) aby uzyskać instrukcje.

   Podczas tworzenia konta laboratorium, może okazać się przydatne do zapoznania się z zasobów platformy Azure zaangażowanych. Aby uzyskać więcej informacji zobacz następujące artykuły:

   - [Subskrypcja](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Grupa zasobów](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Konto laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Laboratorium w klasie](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Wybieranie regionu i lokalizacji](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Wskazówki dotyczące nazewnictwa zasobów](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Dodaj użytkowników do roli twórcy laboratorium.** Aby uzyskać instrukcje, zobacz [dodawanie użytkowników do roli twórcy laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).

   Ponadto, aby uzyskać więcej informacji na temat różnych ról, które mogą być przypisane do użytkowników, którzy będą zarządzać kontami laboratorium i laboratoriów, zobacz [przewodnik na temat zarządzania tożsamością](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Połącz się z siecią wirtualną równorzędną.** Aby uzyskać instrukcje, zobacz [łączenie sieci laboratorium z równorzędną siecią wirtualną](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Może być również konieczne zapoznanie się z instrukcjami [dotyczącymi konfigurowania zakresu adresów maszyn wirtualnych w laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Włączanie i przeglądanie obrazów.** Aby uzyskać instrukcje, zobacz [włączanie obrazów usługi Azure Marketplace dla twórców laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Aby przejrzeć zawartość każdego obrazu w portalu Azure Marketplace, wybierz nazwę obrazu. Na przykład na poniższym zrzucie ekranu przedstawiono szczegóły obrazu maszyny wirtualnej Ubuntu Data Science:

   ![Zrzut ekranu przedstawiający przeglądanie obrazów witryny Azure Marketplace](../media/setup-guide/review-marketplace-images.png)

   Jeśli do konta laboratorium jest dołączona galeria obrazów udostępnionych i chcesz włączyć udostępnianie obrazów niestandardowych przez twórców laboratoriów, wykonaj czynności podobne do tych pokazanych na poniższym zrzucie ekranu:

   ![Zrzut ekranu przedstawiający włączanie obrazów niestandardowych w galerii obrazów udostępnionych](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie kontami laboratorium](how-to-manage-lab-accounts.md)

- [Przewodnik po konfiguracji laboratorium w klasie](setup-guide.md)
