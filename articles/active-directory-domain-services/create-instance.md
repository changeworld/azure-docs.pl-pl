---
title: 'Azure Active Directory Domain Services: Wprowadzenie | Dokumentacja firmy Microsoft'
description: Włączanie usługi Azure Active Directory Domain Services w witrynie Azure portal
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: ergreenl
ms.openlocfilehash: 637ad62744affa37630df9c841f3c7529674e788
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246332"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Włączanie usługi Azure Active Directory Domain Services w witrynie Azure portal
W tym artykule przedstawiono sposób włączania Azure Active Directory Domain Services (Azure AD DS) w witrynie Azure portal.


## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, potrzebne są:

* Nieprawidłowy **subskrypcji platformy Azure**.
* **Katalog usługi Azure AD** — albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
* **Subskrypcji platformy Azure musi być skojarzony z katalogiem Azure AD**.
* Potrzebujesz **administratora globalnego** uprawnień w katalogu usługi Azure AD, aby włączyć usługi domenowe Azure AD.


## <a name="enable-azure-ad-domain-services"></a>Włączanie Usług domenowych Azure AD

Aby uruchomić **włączyć usługi Azure AD Domain Services** kreatora, wykonaj następujące czynności:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. W okienku po lewej stronie kliknij pozycję **Utwórz zasób**.
3. W **New** wpisz **usługi domenowe** na pasku wyszukiwania.

    ![Wyszukiwanie usług domain services](./media/getting-started/search-domain-services.png)

4. Kliknij, aby wybrać **usług domenowych Azure AD** z listy sugestii dotyczących wyszukiwania. Na **usług domenowych Azure AD** kliknij **Utwórz** przycisku.

    ![Domena usługi: widok](./media/getting-started/domain-services-blade.png)

5. **Włączyć usługi Azure AD Domain Services** uruchomić kreatora.


## <a name="task-1-configure-basic-settings"></a>Zadanie 1. Konfigurowanie ustawień podstawowych
W **podstawy** strona kreatora określ nazwę domeny DNS dla domeny zarządzanej. Możesz również grupę zasobów i lokalizacji platformy Azure, do którego powinny zostać wdrożone domeny zarządzanej.

![Konfigurowanie podstawy](./media/getting-started/domain-services-blade-basics.png)

1. Wybierz **nazwy domeny DNS** dla domeny zarządzanej.

   > [!NOTE]
   > **Wskazówki dotyczące wybierania nazwy domeny DNS**
   > * **Nazwa domeny wbudowane:** Domyślnie Kreator określa nazwy domen wbudowana/domyślnego katalogu (przy użyciu **. onmicrosoft.com** sufiks) dla Ciebie. Jeśli wybierzesz umożliwić dostęp do domeny zarządzanej secure LDAP przez internet, należy się spodziewać problemy, tworzenie publiczny Rekord DNS i uzyskiwanie bezpiecznego certyfikatu LDAP z publicznego urzędu certyfikacji dla tej nazwy domeny. Firma Microsoft posiada *. onmicrosoft.com* domeny i urzędy certyfikacji nie będzie wystawiać certyfikatów zagwarantowanie dla tej domeny.
   > * **Niestandardowe nazwy domen:** Można także wpisać niestandardową nazwę domeny. W tym przykładzie niestandardową nazwą domeny jest *contoso100.com*.
   > * **Sufiksy domeny bez obsługi routingu:** Ogólnie zaleca się unikanie sufiks nazwy domeny bez obsługi routingu. Na przykład zaleca się unikanie tworzenia domeny przy użyciu nazwy domeny DNS "contoso.local". Sufiks DNS ".local" nie podlega routingowi i mogą powodować problemy rozpoznawania nazw DNS.
   > * **Ograniczenia prefiks domeny:** Prefiks określony dla nazwy domeny (przykładowo *contoso100* w nazwie domeny *contoso100.com*) nie może zawierać więcej niż 15 znaków. Nie można utworzyć domeny zarządzanej, z prefiksem dłuższa niż 15 znaków.
   > * **Wystąpił konflikt między nazwą sieci:** Upewnij się, że nazwa domeny DNS wybrana dla domeny zarządzanej nie istnieje już w sieci wirtualnej. W szczególności sprawdź, czy:
   >     * Masz już domeny usługi Active Directory, z tą samą nazwą domeny DNS w sieci wirtualnej.
   >     * Sieć wirtualna, której zamierzasz włączyć domena zarządzana ma połączenie sieci VPN z siecią lokalną. W tym scenariuszu upewnij się, że nie masz domenę z tą samą nazwą domeny DNS w sieci lokalnej.
   >     * masz istniejącą usługę w chmurze z tą nazwą w sieci wirtualnej.

2. Wybierz pozycję Azure **subskrypcji** , w której chcesz utworzyć domeny zarządzanej.

3. Wybierz **grupy zasobów** powinny należeć domeny zarządzanej. Wybierz opcję **Utwórz nową** lub **Użyj istniejącej** opcji, aby wybrać grupę zasobów.

4. Wybierz platformę Azure **lokalizacji** , w której powinny zostać utworzone domeny zarządzanej. Na **sieci** stronie kreatora zostanie wyświetlony tylko wirtualne, które należą do wybranej lokalizacji.

5. Kliknij przycisk **OK** można przenieść do **sieci** strony kreatora.


## <a name="next-step"></a>Następny krok
[Task 2: configure network settings (Zadanie 2. Konfigurowanie ustawień sieciowych)](active-directory-ds-getting-started-network.md)
