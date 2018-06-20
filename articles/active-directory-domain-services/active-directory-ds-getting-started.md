---
title: 'Usług domenowych Azure Active Directory: Wprowadzenie | Dokumentacja firmy Microsoft'
description: Włączanie usługi Azure Active Directory Domain Services przy użyciu portalu Azure
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: maheshu
ms.openlocfilehash: 340193f191bbdbe658769f9265f9e63844481c32
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265273"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Włączanie usługi Azure Active Directory Domain Services przy użyciu portalu Azure
W tym artykule przedstawiono sposób włączania usługi Azure Active Directory Domain Services (Azure AD DS) za pomocą portalu Azure.


## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, należy:

* Prawidłowy **subskrypcji platformy Azure**.
* **Katalog usługi Azure AD** -albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
* **Subskrypcji platformy Azure musi być skojarzona z katalogiem Azure AD**.
* Należy **administratora globalnego** uprawnień w katalogu usługi Azure AD, aby włączyć usługi domenowe Azure AD.


## <a name="enable-azure-ad-domain-services"></a>Włączanie Usług domenowych Azure AD

Aby uruchomić **usług włączyć domenowych Azure AD** kreatora, wykonaj następujące czynności:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. W okienku po lewej stronie kliknij pozycję **Utwórz zasób**.
3. W **nowy** wpisz **usług domenowych w usłudze** na pasku wyszukiwania.

    ![Wyszukaj usług domenowych w usłudze](./media/getting-started/search-domain-services.png)

4. Kliknij, aby wybrać **usług domenowych Azure AD** z listy sugestii dotyczących wyszukiwania. Na **usług domenowych Azure AD** kliknij przycisk **Utwórz** przycisku.

    ![Domena usługi: widok](./media/getting-started/domain-services-blade.png)

5. **Usług włączyć domenowych Azure AD** uruchomić kreatora.


## <a name="task-1-configure-basic-settings"></a>Zadanie 1: Konfigurowanie ustawień podstawowych
W **podstawy** strona kreatora określ nazwy domeny DNS dla domeny zarządzanej. Można także grupy zasobów i lokalizacja platformy Azure, do którego powinny zostać wdrożone domeny zarządzanej.

![Skonfiguruj podstawy](./media/getting-started/domain-services-blade-basics.png)

1. Wybierz **nazwy domeny DNS** dla domeny zarządzanej.

   > [!NOTE]
   > **Wskazówki dotyczące wybierania nazwy domeny DNS**
   > * **Nazwa domeny wbudowanych:** domyślnie, Kreator Określa nazwę domeny wbudowana/domyślnego katalogu (z **. onmicrosoft.com** sufiks) dla Ciebie. Jeśli wybierzesz włączyć bezpieczny dostęp LDAP do domeny zarządzanej za pośrednictwem Internetu, należy spodziewać się problemów tworzenia publiczny Rekord DNS lub uzyskiwania certyfikatu bezpiecznego LDAP z publicznego urzędu certyfikacji dla tej nazwy domeny. Microsoft, właścicielem *. onmicrosoft.com* domeny i urzędy certyfikacji nie będzie wystawiać certyfikaty zagwarantowanie dla tej domeny.
   * **Niestandardowa nazwa domeny:** możesz także wpisać w niestandardowej nazwy domeny. W tym przykładzie niestandardową nazwą domeny jest *contoso100.com*.
   * **Sufiksy domen bez obsługi routingu:** ogólnie zaleca się unikanie sufiks nazwy domeny bez obsługi routingu. Na przykład lepiej jest Unikaj tworzenia domeny z nazwy domeny DNS "contoso.local". Sufiks DNS ".local" nie podlega routingowi i mogą powodować problemy z rozpoznawaniem nazw DNS.
   * **Ograniczenia domen prefiksu:** prefiks nazwy domeny określonej (na przykład *contoso100* w *contoso100.com* nazwa domeny) może zawierać więcej niż 15 znaków. Nie można utworzyć domeny zarządzanej z prefiksem dłuższa niż 15 znaków.
   * **Konflikty nazw sieciowych:** upewnij się, że nazwa domeny DNS wybrana dla domeny zarządzanej nie istnieje już w sieci wirtualnej. W szczególności, sprawdź, czy:
       * Masz już domenę usługi Active Directory z tą samą nazwą domeny DNS w sieci wirtualnej.
       * Sieć wirtualna, której zamierzasz włączyć domeny zarządzanej ma połączenie sieci VPN z sieci lokalnej. W tym scenariuszu upewnij się, że nie masz domenę z tą samą nazwą domeny DNS w sieci lokalnej.
       * masz istniejącą usługę w chmurze z tą nazwą w sieci wirtualnej.
    >

2. Wybierz platformy Azure **subskrypcji** , w której chcesz utworzyć domeny zarządzanej.

3. Wybierz **grupy zasobów** powinien należeć domeny zarządzanej. Wybierz **Utwórz nowy** lub **Użyj istniejącego** opcji, aby wybrać grupę zasobów.

4. Wybierz platformy Azure **lokalizacji** , w którym ma zostać utworzony domeny zarządzanej. Na **sieci** strony kreatora, zobacz sieci tylko wirtualne, które należą do wybrania lokalizacji.

5. Kliknij przycisk **OK** można przenieść do **sieci** stronie kreatora.


## <a name="next-step"></a>Następny krok
[Task 2: configure network settings (Zadanie 2. Konfigurowanie ustawień sieciowych)](active-directory-ds-getting-started-network.md)
