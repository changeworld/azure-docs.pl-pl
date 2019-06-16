---
title: Konfigurowanie konta laboratorium w usłudze Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować konta laboratorium, po jego utworzeniu.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: ba469c038f04a31a57e798b97b5120bec573feae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65414041"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Konfigurowanie konta laboratorium w usłudze Azure Lab Services 
W usługach Azure Lab Services konta laboratorium jest kontenerem dla typów laboratorium zarządzanych, takich jak laboratorium na potrzeby zajęć. Administrator konfiguruje konta laboratorium przy użyciu usługi Azure Lab Services i zapewnia dostęp do laboratorium właścicieli, którzy mogą tworzyć laboratoriów w ramach konta. W tym artykule opisano sposób tworzenia konta laboratorium, są wyświetlane wszystkie konta laboratorium lub usuwanie konta laboratorium.

## <a name="connect-with-a-peer-virtual-network"></a>Połącz się z równorzędnej sieci wirtualnej
Aby połączyć sieć wirtualna jako sieć równorzędna z siecią wirtualną laboratorium, wykonaj następujące kroki:

1. Na **konta laboratorium** wybierz opcję **konfiguracji Labs** w menu po lewej stronie.

    ![Strona konfiguracji Labs](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Dla **równorzędna sieć wirtualna**, wybierz opcję **włączone** lub **wyłączone**. Wartość domyślna to **wyłączone**. Aby włączyć równorzędnej sieci wirtualnej, wykonaj następujące czynności: 
    1. Wybierz **włączone**.
    2. Wybierz **sieci wirtualnej** z listy rozwijanej. 
3. Wybierz pozycję **Zapisz** na pasku narzędzi. 

Laboratoria utworzony na tym koncie są podłączone do wybranej sieci wirtualnej. Mogą oni dostęp do zasobów w wybranej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [połączyć sieć środowiska laboratoryjnego z równorzędnej sieci wirtualnej w usłudze Azure Lab Services](how-to-connect-peer-virtual-network.md).

Po wybraniu sieci wirtualnej dla **równorzędna sieć wirtualna** pola **twórca laboratorium Zezwalaj, aby wybrać lokalizację lab** opcja jest wyłączona. Jest to spowodowane laboratoriów w ramach konta laboratorium musi znajdować się w tym samym regionie co konto usługi laboratorium dla nich nawiązywanie połączeń z zasobami w równorzędnej sieci wirtualnej. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Zezwalaj na Twórca laboratorium wybrać lokalizację dla laboratorium
Możesz zezwolić twórca laboratorium utworzyć laboratoria w innej lokalizacji niż lokalizacja konta laboratorium, wykonując następujące czynności: 

1. Na **konta laboratorium** wybierz opcję **konfiguracji Labs** w menu po lewej stronie.
2. Dla **twórca laboratorium Zezwalaj, aby wybrać lokalizację lab**, wybierz opcję **włączone** chcącym twórca laboratorium, aby można było wybrać lokalizację dla laboratorium. Jeśli jest ono wyłączone, laboratoria są tworzone automatycznie w tej samej lokalizacji, w której istnieje konto laboratorium. 
    
    To pole jest wyłączone, po wybraniu sieci wirtualnej dla **równorzędna sieć wirtualna** pola. Jest to spowodowane laboratoriów w ramach konta laboratorium musi znajdować się w tym samym regionie co konto usługi laboratorium dla nich dostępu do zasobów w równorzędnej sieci wirtualnej. 
1. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Ustawienia lokalizacji laboratorium](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Określ zakres adresów dla maszyn wirtualnych w laboratorium
Poniższa procedura zawiera kroki, aby określić zakres adresów dla maszyn wirtualnych w środowisku laboratoryjnym. Jeśli zaktualizujesz zakres, który wcześniej określony, zakres adresów zmodyfikowane dotyczą tylko maszyn wirtualnych, które są tworzone po wprowadzeniu zmian. 

Poniżej przedstawiono pewne ograniczenia, określając zakres adresów, które należy mieć na uwadze. 

- Prefiks musi być mniejszy niż lub równy do 23. 
- Jeśli sieć wirtualna a jest połączona z kontem laboratorium, zakres podany adres nie może pokrywać się z zakresem adresów z wirtualnej sieci równorzędnej.

1. Na **konta laboratorium** wybierz opcję **konfiguracji Labs** w menu po lewej stronie.
2. Aby uzyskać **zakres adresów** Określ zakres adresów dla maszyn wirtualnych, które zostaną utworzone w środowisku laboratoryjnym. Zakres adresów musi należeć do notacji bezklasowego routingu międzydomenowego (CIDR) (przykład: 10.20.0.0/23). Maszyny wirtualne w laboratorium, zostaną utworzone w tym zakresie adresów.
3. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Skonfigurować zakres adresów](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Dodawanie użytkownika do roli twórcy laboratorium
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, użytkownik musi być członkiem roli **Twórca laboratorium** na koncie laboratorium. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do tej roli. Jeśli planujesz użyć tego samego konta użytkownika w celu utworzenia laboratorium na potrzeby zajęć, możesz pominąć ten krok. Aby użyć innego konta użytkownika do utworzenia laboratorium na potrzeby zajęć, wykonaj następujące czynności: 

Aby przyznać nauczycielom uprawnienie do tworzenia laboratoriów na potrzeby zajęć, dodaj ich do roli **Twórca laboratorium**:

1. Na stronie **Konto laboratorium** wybierz pozycję **Kontrola dostępu (Zarządzanie dostępem i tożsamościami)** i kliknij pozycję **+ Dodaj przypisanie roli** na pasku narzędzi. 

    ![Kontrola dostępu -> przycisk Dodaj przypisanie roli](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na stronie **Dodawanie przypisania roli** wybierz pozycję **Twórca laboratorium** w obszarze **Rola**, wybierz użytkownika do dodania do roli Twórcy laboratorium, a następnie wybierz pozycję **Zapisz**. 

    ![Dodawanie twórcy laboratorium](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Określanie obrazów w portalu Marketplace dostępnych dla twórców laboratorium
Jako właściciel konta laboratorium możesz określić obrazy witryny Marketplace, których twórcy laboratorium mogą używać do tworzenia laboratoriów na koncie laboratorium. 

1. W menu po lewej stronie wybierz pozycję **Obrazy w portalu Marketplace**. Domyślnie zobaczysz pełną listę obrazów (włączonych i wyłączonych). Tę listę można filtrować, aby wyświetlić tylko obrazy włączone/wyłączone, wybierając z listy rozwijanej u góry opcję **Tylko włączone**/**Tylko wyłączone**. 
    
    ![Strona Obrazy w portalu Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    Obrazy witryny Marketplace wyświetlane na liście to tylko obrazy spełniające następujące warunki:
        
    - Tworzą jedną maszynę wirtualną
    - Używają usługi Azure Resource Manager do aprowizowania maszyn wirtualnych
    - Nie wymagają zakupu dodatkowego planu licencjonowania
2. Aby w portalu Marketplace **wyłączyć** obraz, który zostały włączony, wykonaj jedną z następujących czynności: 
    1. Wybierz pozycję **... (wielokropek)** w ostatniej kolumnie, a następnie polecenie **Wyłącz obraz**. 

        ![Wyłączanie jednego obrazu](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Wybierz jeden lub większą liczbę obrazów z listy przez zaznaczenie pól wyboru przed nazwami obrazów, a następnie wybierz pozycję **Wyłącz wybrane obrazy**. 

        ![Wyłączanie wielu obrazów](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. Podobnie, aby w portalu Marketplace **włączyć** obraz, wykonaj jedną z następujących czynności: 
    1. Wybierz pozycję **... (wielokropek)** w ostatniej kolumnie, a następnie polecenie **Włącz obraz**. 
    2. Wybierz jeden lub większą liczbę obrazów z listy przez zaznaczenie pól wyboru przed nazwami obrazów, a następnie wybierz pozycję **Włącz wybrane obrazy**. 




## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium dostęp do laboratoriów na potrzeby zajęć](how-to-use-classroom-lab.md)
