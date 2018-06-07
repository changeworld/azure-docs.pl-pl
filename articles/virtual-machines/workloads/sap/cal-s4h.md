---
title: Wdrożenie SAP S/4HANA lub BW/4HANA na maszynie Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Wdrożenie SAP S/4HANA lub BW/4HANA na maszynie Wirtualnej platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
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
ms.openlocfilehash: 10c5116afa46817a42834e0350937fde7ae0b927
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657346"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Wdrożenie SAP S/4HANA lub BW/4HANA na platformie Azure
W tym artykule opisano sposób wdrażania S/4HANA na platformie Azure przy użyciu biblioteki urządzenia chmury SAP (SAP CAL) 3.0. Aby wdrożyć inne SAP HANA rozwiązań, takich jak BW/4HANA, wykonaj te same kroki.

> [!NOTE]
Aby uzyskać więcej informacji o licencji CAL SAP, przejdź do [biblioteki urządzenia chmury SAP](https://cal.sap.com/) witryny sieci Web. SAP ma również blog o [SAP chmury urządzenia biblioteki 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

> [!NOTE]
Począwszy od 29 maja 2017 modelu wdrażania usługi Azure Resource Manager oprócz preferowane mniej klasycznym modelu wdrażania służy do wdrażania SAP CAL. Firma Microsoft zaleca używanie nowego modelu wdrażania usługi Resource Manager i pominąć klasycznym modelu wdrażania.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Krok po kroku proces wdrażania rozwiązania

Następująca sekwencja zrzuty ekranu przedstawia sposób wdrażania S/4HANA na platformie Azure przy użyciu SAP CAL. Proces działa tak samo dla innych rozwiązań, takich jak BW/4HANA.

**Rozwiązań** strona przedstawia niektóre z dostępnych rozwiązań opartych na SAP CAL HANA na platformie Azure. **SAP S/4HANA 1610 FPS01, urządzenia Fully-Activated** znajduje się w środkowej wierszu:

![Rozwiązania CAL SAP](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Tworzenie konta w SAP CAL
1. Zaloguj się do SAP CAL po raz pierwszy, należy użyć programu SAP S-użytkownika lub inny użytkownik zarejestrowany w usłudze SAP. Następnie zdefiniuj SAP CAL konta, które jest używane przez SAP CAL do wdrażania urządzeń na platformie Azure. W definicji konta musisz:

    a. Wybierz model wdrażania na platformie Azure (Resource Manager lub classic).

    b. Wprowadź subskrypcji platformy Azure. Konto SAP CAL można przypisać do tylko jednej subskrypcji. Jeśli potrzebujesz więcej niż jedną subskrypcję, musisz utworzyć nowe konto SAP CAL.

    c. Nadaj uprawnienia SAP CAL do wdrożenia w ramach subskrypcji platformy Azure.

    > [!NOTE]
    W następnych krokach przedstawiono sposób tworzenia konta SAP CAL dla wdrożenia usługi Resource Manager. Jeśli masz już konto SAP CAL połączonego z klasycznym modelu wdrożenia, możesz *muszą* wykonać następujące kroki, aby utworzyć nowe konto SAP CAL. Nowe konto SAP CAL musi wdrożyć w modelu usługi Resource Manager.

2. Utwórz nowe konto SAP CAL. **Kont** strony zawiera trzy opcje dla platformy Azure: 

    a. **Microsoft Azure (klasyczne)** jest klasycznym modelu wdrażania i nie jest preferowany.

    b. **Microsoft Azure** jest nowy model wdrażania Menedżera zasobów.

    c. **Windows Azure obsługiwany przez 21Vianet** jest opcją w Chinach, która używa klasycznego modelu wdrażania.

    Aby wdrożyć w modelu usługi Resource Manager, wybierz **Microsoft Azure**.

    ![Szczegóły konta CAL SAP](./media/cal-s4h/s4h-pic-2a.png)

3. Wprowadź Azure **identyfikator subskrypcji** znajdującymi się w portalu Azure.

   ![Konta CAL SAP](./media/cal-s4h/s4h-pic3c.png)

4. Aby autoryzować CAL SAP do wdrożenia w subskrypcji platformy Azure, zdefiniowaną, kliknij przycisk **autoryzacji**. Na karcie przeglądarki jest wyświetlana następująca strona:

   ![Logowania usług w chmurze programu Internet Explorer](./media/cal-s4h/s4h-pic4c.png)

5. Jeśli jest wymieniona więcej niż jednego użytkownika, wybierz konto Microsoft, która jest połączona coadministrator subskrypcji Azure, które wybrano. Na karcie przeglądarki jest wyświetlana następująca strona:

   ![Potwierdzenie usług w chmurze programu Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

6. Kliknij przycisk **zaakceptować**. Jeśli autoryzacja zakończy się pomyślnie, SAP CAL definicji konta zostaną wyświetlone ponownie. Po pewnym czasie wyświetli się komunikat potwierdzający, pomyślnego procesu autoryzacji.

7. Aby przypisać nowo utworzonych kont SAP CAL do użytkownika, wprowadź użytkownika **identyfikator użytkownika** w polu tekstowym w prawo, a następnie kliknij **Dodaj**.

   ![Konta do skojarzenia użytkownika](./media/cal-s4h/s4h-pic8a.png)

8. Aby skojarzyć konto użytkownika, którego używasz do logowania się na SAP CAL, kliknij przycisk **przeglądu**. 
 
9. Aby utworzyć skojarzenie użytkownika z nowo utworzonego konta SAP CAL, kliknij przycisk **Utwórz**.

   ![Użytkownikowi na skojarzenie konta SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Pomyślnie utworzono konto SAP CAL, które jest w stanie:

- Użyj modelu wdrażania usługi Resource Manager.
- Wdrażanie systemów SAP do Twojej subskrypcji platformy Azure.

Teraz można rozpocząć wdrażanie S/4HANA w subskrypcji użytkownika na platformie Azure.

> [!NOTE]
Przed kontynuowaniem należy sprawdzić, czy Azure vCPU przydziały dla maszyn wirtualnych platformy Azure H serii. W tej chwili SAP CAL używa H serii maszyn wirtualnych Azure do wdrożenia niektórych rozwiązań opartych na SAP HANA. Subskrypcji platformy Azure może nie mieć żadnych przydziały vCPU H-Series H serii. Jeśli tak, może być konieczne skontaktuj się z pomocą techniczną platformy Azure można pobrać przydziału co najmniej 16 Vcpu H serii.

> [!NOTE]
Podczas wdrażania rozwiązania na platformie Azure w SAP CAL, może się okazać, że można wybrać tylko jeden region platformy Azure. Aby wdrożyć w regionach platformy Azure, innego niż zasugerowany przez SAP CAL, należy kupić subskrypcję CAL z programu SAP. Należy również otworzyć wiadomość z SAP Twoje konto CAL włączone do dostarczania w regionach platformy Azure innych niż początkowo sugerowane.

### <a name="deploy-a-solution"></a>Wdrażanie rozwiązania

Ta funkcja pozwala wdrożyć rozwiązanie z **rozwiązań** strony SAP CAL. SAP CAL ma dwie sekwencje wdrażania:

- Podstawowe sekwencji, która używa jedną stronę w celu zdefiniowania wdrożenie systemu
- Zaawansowane sekwencji, zapewniająca niektórych opcji rozmiarów maszyny Wirtualnej 

Przedstawiony podstawowa ścieżka do wdrożenia w tym miejscu.

1. Na **szczegóły konta** strony, musisz:

    a. Wybierz konto SAP CAL. (Użyj konta, które jest skojarzony z wdrożyć przy użyciu modelu wdrażania usługi Resource Manager).

    b. Wprowadź wystąpienia **nazwa**.

    c. Wybierz platformy Azure **Region**. SAP CAL sugeruje regionu. Jeśli nie masz subskrypcji SAP CAL wymagają innego regionu Azure, należy zamówić subskrypcję CAL z SAP.

    d. Wprowadź wzorzec **hasło** dla rozwiązania dziewięć osiem znaków. Hasło jest używany dla administratorów różnych składników.

   ![Tryb CAL Basic SAP: Utworzenie wystąpienia](./media/cal-s4h/s4h-pic10a.png)

2. Kliknij przycisk **Utwórz**, a w wyświetlonym oknie komunikatu kliknij **OK**.

   ![SAP CAL obsługiwane rozmiary maszyn wirtualnych](./media/cal-s4h/s4h-pic10b.png)

3. W **klucza prywatnego** okno dialogowe, kliknij przycisk **przechowywania** do przechowywania klucza prywatnego w SAP CAL. Aby korzystać z ochrony hasłem dla klucza prywatnego, kliknij przycisk **Pobierz**. 

   ![Klucz prywatny CAL SAP](./media/cal-s4h/s4h-pic10c.png)

4. Przeczytaj SAP CAL **ostrzeżenie** komunikatów, a następnie kliknij przycisk **OK**.

   ![Ostrzeżenie CAL SAP](./media/cal-s4h/s4h-pic10d.png)

    Teraz wdrożenie ma miejsce. Po pewnym czasie w zależności od rozmiaru i złożoności rozwiązania (SAP CAL zapewnia szacunkową), zostanie wyświetlony stan jako aktywne i gotowe do użycia.

5. Aby znaleźć maszyn wirtualnych zebrane ze skojarzonymi zasobami w jednej grupie zasobów, przejdź do portalu Azure: 

   ![Wdrożone w portalu nowe obiekty SAP CAL](./media/cal-s4h/sapcaldeplyment_portalview.png)

6. W portalu SAP CAL, stan jest wyświetlany jako **Active**. Aby połączyć się rozwiązania, kliknij przycisk **Connect**. Inne opcje, aby połączyć się z różnych składników są wdrażane w ramach tego rozwiązania.

   ![Wystąpienia CAL SAP](./media/cal-s4h/active_solution.png)

7. Przed użyciem jedną z opcji łączenia się z systemami wdrożone, kliknij przycisk **Getting Started Guide**. 

   ![Połącz się z wystąpieniem](./media/cal-s4h/connect_to_solution.png)

    Dokumentację nazwy użytkowników, dla każdej z metod łączności. Hasła dla tych użytkowników ustawiono hasło główne, zdefiniowanych na początku procesu wdrażania. W dokumentacji innym użytkownikom bardziej funkcjonalny są wyświetlane z haseł, których można używać do logowania się na wdrożonym systemie. 

    Na przykład jeśli używasz SAP graficznego interfejsu użytkownika, który jest preinstalowany na tym komputerze pulpitu zdalnego systemu S/4 może wyglądać następująco:

   ![SM50 w interfejsie GUI preinstalowane SAP](./media/cal-s4h/gui_sm50.png)

    Lub jeśli używasz DBACockpit wystąpienie może wyglądać następująco:

   ![SM50 w interfejsie GUI DBACockpit SAP](./media/cal-s4h/dbacockpit.png)

W ciągu kilku godzin dobrej kondycji urządzenia SAP S/4 jest wdrażana na platformie Azure.

Jeśli zakupiono subskrypcję SAP CAL SAP w pełni obsługuje wdrożeniami SAP CAL na platformie Azure. Kolejka pomocy technicznej jest BC-VCM-licencji dostępu klienta.







