---
title: Wdrażanie rozwiązania SAP S/4HANA lub BW/4HANA na Maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Wdrażanie rozwiązania SAP S/4HANA lub BW/4HANA na Maszynie wirtualnej platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 65643352a269796fc5353ff4cd0cb15d5f1502ec
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707477"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Wdrażanie rozwiązania SAP S/4HANA lub BW/4HANA na platformie Azure
W tym artykule opisano sposób wdrażania oprogramowania S/4HANA na platformie Azure przy użyciu biblioteki SAP Cloud Appliance Library (SAP CAL) 3.0. Aby wdrożyć innych rozwiązań opartych na oprogramowanie SAP HANA, takich jak BW/4HANA, wykonaj te same czynności.

> [!NOTE]
> Aby uzyskać więcej informacji na temat SAP CAL, przejdź do [SAP Cloud Appliance Library](https://cal.sap.com/) witryny sieci Web. Firma SAP ma również blog o [SAP Cloud Appliance biblioteki 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> Od 29 maja 2017 r. modelu wdrażania usługi Azure Resource Manager, oprócz preferowane mniej klasycznego modelu wdrażania można użyć do wdrożenia SAP CAL. Firma Microsoft zaleca użycie nowego modelu wdrażania usługi Resource Manager, a następnie pominąć klasycznego modelu wdrażania.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Instrukcje krok po kroku proces wdrażania rozwiązania

Poniższa sekwencja zrzuty ekranu pokazuje, jak wdrażanie oprogramowania S/4HANA na platformie Azure przy użyciu SAP CAL. Proces działa tak samo dla innych rozwiązań, takich jak BW/4HANA.

**Rozwiązania** strona zawiera niektóre z dostępnych rozwiązań opartych na SAP CAL HANA na platformie Azure. **Oprogramowanie SAP S/4HANA 1610 FPS01, Fully-Activated urządzenia** znajduje się w środkowym rzędzie:

![Rozwiązania programu SAP CAL](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Tworzenie konta usługi w SAP CAL
1. Aby zalogować się do SAP CAL po raz pierwszy, użyj SAP S-użytkownika lub inny użytkownik zarejestrowany z oprogramowaniem SAP. Następnie zdefiniuj SAP CAL konta, które jest używane przez SAP CAL do wdrażania urządzeń na platformie Azure. W definicji konta należy:

    a. Wybierz model wdrażania na platformie Azure (Resource Manager lub model klasyczny).

    b. Wprowadź swoją subskrypcję platformy Azure. Konto SAP CAL można przypisać do tylko jednej subskrypcji. Jeśli potrzebujesz więcej niż jedną subskrypcję, musisz utworzyć nowe konto SAP CAL.

    c. Nadaj uprawnienia SAP CAL do wdrożenia w ramach subskrypcji platformy Azure.

   > [!NOTE]
   >  Następne kroki pokazują jak utworzyć konto SAP CAL dla wdrożeń usługi Resource Manager. Jeśli masz już konto SAP CAL, który jest połączony z klasycznego modelu wdrażania, możesz *muszą* wykonaj następujące kroki, aby utworzyć nowe konto SAP CAL. Nowe konto SAP CAL musi wdrożyć w modelu usługi Resource Manager.

1. Utwórz nowe konto SAP CAL. **Kont** strona zawiera trzy opcje dla platformy Azure: 

    a. **Microsoft Azure (wersja klasyczna)** jest klasycznego modelu wdrażania i preferowana.

    b. **Microsoft Azure** jest nowy model wdrażania usługi Resource Manager.

    c. **Windows Azure, obsługiwana przez firmę 21Vianet** jest opcją w Chinach, który używa klasycznego modelu wdrażania.

    Aby wdrożyć w modelu usługi Resource Manager, wybierz **Microsoft Azure**.

    ![Szczegóły konta CAL SAP](./media/cal-s4h/s4h-pic-2a.png)

1. Wprowadź Azure **identyfikator subskrypcji** znajdującymi się w witrynie Azure portal.

   ![SAP CAL, kont](./media/cal-s4h/s4h-pic3c.png)

1. Aby autoryzować SAP CAL do wdrożenia w subskrypcji platformy Azure zdefiniowany, kliknij przycisk **Autoryzuj**. Na karcie przeglądarki zostanie wyświetlona następująca strona:

   ![Usługi w chmurze programu Internet Explorer Zaloguj się](./media/cal-s4h/s4h-pic4c.png)

1. Jeśli więcej niż jeden użytkownik ma na liście, wybierz konto Microsoft, które jest połączone współadministrator subskrypcji platformy Azure, które wybrano. Na karcie przeglądarki zostanie wyświetlona następująca strona:

   ![Potwierdzenie usług w chmurze programu Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

1. Kliknij przycisk **zaakceptować**. Autoryzacja zakończy się pomyślnie, definicja konta SAP CAL zostanie ponownie wyświetla. Po pewnym czasie wyświetli się komunikat potwierdzający, że proces autoryzacji zakończyło się pomyślnie.

1. Aby przypisać nowo utworzonego konta SAP CAL na użytkownika, wprowadź swoje **identyfikator użytkownika** w polu tekstowym z prawej strony i kliknij przycisk **Dodaj**.

   ![Konta do skojarzenia użytkownika](./media/cal-s4h/s4h-pic8a.png)

1. Aby skojarzyć swoje konto użytkownika, którego używasz do logowania się na SAP CAL, kliknij przycisk **przeglądu**. 
 
1. Aby utworzyć skojarzenie między użytkownika a nowo utworzonego konta SAP CAL, kliknij przycisk **Utwórz**.

   ![Użytkownikowi na skojarzenie konta SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Pomyślnie utworzono konto SAP CAL, który jest w stanie:

- Przy użyciu modelu wdrażania usługi Resource Manager.
- Wdrażanie systemów SAP w ramach subskrypcji platformy Azure.

Teraz możesz rozpocząć wdrażanie oprogramowania S/4HANA w ramach subskrypcji użytkownika na platformie Azure.

> [!NOTE]
> Przed kontynuowaniem należy ustalić, czy limity przydziału procesorów wirtualnych platformy Azure dla maszyn wirtualnych serii H platformy Azure. W chwili do wdrażania, niektóre z rozwiązań opartych na oprogramowanie SAP HANA SAP CAL korzysta serii H maszyn wirtualnych platformy Azure. Twoja subskrypcja platformy Azure może nie mieć żadnych limity przydziału procesorów wirtualnych serii H serii H. Jeśli tak, może być konieczne skontaktuj się z działem pomocy technicznej platformy Azure można pobrać limitu przydziału w co najmniej 16 procesorów wirtualnych serii H.
> 
> [!NOTE]
> Gdy wdrażasz rozwiązania na platformie Azure w SAP CAL, może się okazać, które można wybrać tylko jeden region platformy Azure. Do wdrożenia w regionach platformy Azure w innym niż zaproponowana przez SAP CAL, należy zakupić subskrypcję licencji CAL programu SAP. Ponadto konieczne może być Otwórz wiadomość z oprogramowaniem SAP Twojego konta CAL włączone, aby dostarczyć do regionów platformy Azure innych niż początkowo zalecane.

### <a name="deploy-a-solution"></a>Wdrażanie rozwiązania

Zajmijmy się wdrożeniem rozwiązania z **rozwiązania** strony SAP CAL. SAP CAL ma dwie sekwencje do wdrożenia:

- Podstawowe sekwencji, który używa jednej strony, aby zdefiniować wdrożenie systemu
- Zaawansowane sekwencji, która zawiera niektóre opcje dotyczące rozmiarów maszyn wirtualnych 

Pokażemy, podstawowe ścieżki do wdrożenia w tym miejscu.

1. Na **szczegóły konta** strony, należy:

    a. Wybierz konto SAP CAL. (Użyj konta, które jest skojarzone do wdrożenia przy użyciu modelu wdrażania usługi Resource Manager).

    b. Wprowadź wystąpienie **nazwa**.

    c. Wybierz platformę Azure **Region**. SAP CAL sugeruje region. Jeśli potrzebujesz innego regionu platformy Azure i nie masz subskrypcji SAP CAL, należy zamówić subskrypcję z oprogramowaniem SAP CAL.

    d. Wprowadź wzorzec **hasło** rozwiązania ośmiu lub dziewięciu znaków. Hasło jest używane dla administratorów różnych składników.

   ![SAP CAL Basic tryb: Tworzenie wystąpienia](./media/cal-s4h/s4h-pic10a.png)

1. Kliknij przycisk **Utwórz**i w wyświetlonym oknie komunikatu kliknij **OK**.

   ![SAP CAL obsługiwane rozmiary maszyn wirtualnych](./media/cal-s4h/s4h-pic10b.png)

1. W **klucza prywatnego** okno dialogowe, kliknij przycisk **Store** przechowywanie klucza prywatnego w SAP CAL. Aby użyć hasło ochrony klucza prywatnego, kliknij **Pobierz**. 

   ![Klucz prywatny CAL SAP](./media/cal-s4h/s4h-pic10c.png)

1. Przeczytaj SAP CAL **ostrzeżenie** wiadomości, a następnie kliknij przycisk **OK**.

   ![Ostrzeżenie o SAP CAL](./media/cal-s4h/s4h-pic10d.png)

    Teraz wdrażanie odbywa się. Po pewnym czasie w zależności od rozmiaru i złożoność rozwiązania (SAP CAL zapewnia oszacowanie), zostanie wyświetlony stan jako aktywne i gotowe do użycia.

1. Aby znaleźć maszyny wirtualne, zebrane ze skojarzonymi zasobami w jednej grupie zasobów, przejdź do witryny Azure portal: 

   ![Obiekty SAP CAL wdrożone w nowym portalu](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. W portalu SAP CAL stan jest wyświetlany jako **Active**. Aby połączyć się z rozwiązaniem, kliknij **Connect**. Inne opcje, aby nawiązać połączenie z różnych składników są wdrażane w ramach tego rozwiązania.

   ![Wystąpienia SAP CAL](./media/cal-s4h/active_solution.png)

1. Zanim użyjesz jedną z opcji, aby nawiązać połączenie wdrożonych systemów, kliknij przycisk **Getting Started Guide**. 

   ![Połącz z wystąpieniem programu](./media/cal-s4h/connect_to_solution.png)

    Dokumentacja nazwy użytkowników dla wszystkich metod łączności. Hasło główne, zdefiniowanych na początku procesu wdrażania można ustawić hasła dla tych użytkowników. W dokumentacji innym użytkownikom bardziej funkcjonalnego są wyświetlane z haseł, których można użyć do logowania się na wdrożonym systemie. 

    Na przykład jeśli używasz SAP graficznego interfejsu użytkownika, który jest wstępnie zainstalowane na komputerze Windows pulpitu zdalnego systemu S/4 może wyglądać następująco:

   ![SM50 w wstępnie zainstalowane oprogramowanie SAP graficznego interfejsu użytkownika](./media/cal-s4h/gui_sm50.png)

    Lub jeśli używasz DBACockpit, wystąpienie może wyglądać następująco:

   ![SM50 w interfejsie GUI SAP DBACockpit](./media/cal-s4h/dbacockpit.png)

W ciągu kilku godzin dobrej kondycji urządzenia SAP S/4 jest wdrażana na platformie Azure.

Jeśli zakupiono subskrypcję SAP CAL, SAP w pełni obsługuje wdrożenia za pośrednictwem SAP CAL na platformie Azure. Kolejka pomocy technicznej jest BC-VCM-licencji CAL.







