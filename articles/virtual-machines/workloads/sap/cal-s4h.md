---
title: Wdrażanie sap s/4hana lub BW/4HANA na maszynie wirtualnej platformy Azure | Dokumenty firmy Microsoft
description: Wdrażanie sap s/4hana lub BW/4HANA na maszynie wirtualnej platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: c110a4e0429ba52e01c472097a2241f91d504cf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616205"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Wdrażanie systemu SAP S/4HANA lub BW/4HANA na platformie Azure
W tym artykule opisano sposób wdrażania s/4HANA na platformie Azure przy użyciu biblioteki SAP Cloud Appliance Library (SAP CAL) 3.0. Aby wdrożyć inne rozwiązania oparte na SAP HANA, takie jak BW/4HANA, wykonaj te same kroki.

> [!NOTE]
> Aby uzyskać więcej informacji na temat biblioteki SAP CAL, przejdź do [witryny SAP Cloud Appliance Library.](https://cal.sap.com/) SAP ma również bloga o [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> Od 29 maja 2017 r. można użyć modelu wdrażania usługi Azure Resource Manager oprócz mniej preferowanego modelu wdrażania klasycznego w celu wdrożenia narzędzia SAP CAL. Zaleca się użycie nowego modelu wdrażania Menedżera zasobów i pominięcie klasycznego modelu wdrażania.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Krok po kroku, aby wdrożyć rozwiązanie

Poniższa sekwencja zrzutów ekranu pokazuje, jak wdrożyć S/4HANA na platformie Azure przy użyciu środowiska SAP CAL. Proces ten działa w ten sam sposób w przypadku innych rozwiązań, takich jak BW/4HANA.

Na stronie **Rozwiązania przedstawiono** niektóre rozwiązania oparte na systemie SAP CAL HANA dostępne na platformie Azure. **SAP S/4HANA 1610 FPS01, W pełni aktywowane urządzenie** znajduje się w środkowym rzędzie:

![Rozwiązania SAP CAL](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Tworzenie konta w liczeczce SAP
1. Aby zalogować się do środowiska SAP CAL po raz pierwszy, użyj użytkownika SAP S lub innego użytkownika zarejestrowanego w sap. Następnie zdefiniuj konto SAP CAL, które jest używane przez środowisko SAP CAL do wdrażania urządzeń na platformie Azure. W definicji konta musisz:

    a. Wybierz model wdrażania na platformie Azure (Menedżer zasobów lub klasyczny).

    b. Wprowadź subskrypcję platformy Azure. Konto SAP CAL można przypisać tylko do jednej subskrypcji. Jeśli potrzebujesz więcej niż jednej subskrypcji, musisz utworzyć inne konto SAP CAL.

    d. Nadaj uprawnieniaemu sap CAL do wdrożenia w ramach subskrypcji platformy Azure.

   > [!NOTE]
   >  W następnych krokach pokazano, jak utworzyć konto SAP CAL dla wdrożeń Menedżera zasobów. Jeśli masz już konto SAP CAL, które jest połączone z klasycznym modelem wdrażania, *musisz* wykonać następujące kroki, aby utworzyć nowe konto SAP CAL. Nowe konto SAP CAL musi być wdrożone w modelu Menedżera zasobów.

1. Utwórz nowe konto SAP CAL. Na stronie **Konta** przedstawiono trzy opcje dla platformy Azure: 

    a. **Microsoft Azure (klasyczny)** jest klasycznym modelem wdrażania i nie jest już preferowany.

    b. **Microsoft Azure** to nowy model wdrażania usługi Resource Manager.

    d. **Windows Azure obsługiwany przez 21Vianet** jest opcją w Chinach, która używa klasycznego modelu wdrażania.

    Aby wdrożyć w modelu Menedżera zasobów, wybierz pozycję **Microsoft Azure**.

    ![Szczegóły konta SAP CAL](./media/cal-s4h/s4h-pic-2a.png)

1. Wprowadź **identyfikator subskrypcji** platformy Azure, który można znaleźć w witrynie Azure portal.

   ![Konta SAP CAL](./media/cal-s4h/s4h-pic3c.png)

1. Aby autoryzować zdefiniowaną subskrypcję SAP CAL w celu wdrożenia w zdefiniowanej subskrypcji platformy Azure, kliknij przycisk **Autoryzuj**. Na karcie przeglądarki pojawi się następująca strona:

   ![Logowanie się do usług w chmurze programu Internet Explorer](./media/cal-s4h/s4h-pic4c.png)

1. Jeśli na liście znajduje się więcej niż jeden użytkownik, wybierz konto Microsoft, które jest połączone jako współadministrator wybranej subskrypcji platformy Azure. Na karcie przeglądarki pojawi się następująca strona:

   ![Potwierdzenie usług w chmurze programu Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

1. Kliknij przycisk **Zaakceptuj**. Jeśli autoryzacja zakończy się pomyślnie, definicja konta SAP CAL zostanie wyświetlona ponownie. Po krótkim czasie komunikat potwierdza, że proces autoryzacji zakończył się pomyślnie.

1. Aby przypisać nowo utworzone konto SAP CAL do użytkownika, wprowadź jego **identyfikator użytkownika** w polu tekstowym po prawej stronie i kliknij przycisk **Dodaj**.

   ![Powiązanie konta z użytkownikiem](./media/cal-s4h/s4h-pic8a.png)

1. Aby skojarzyć swoje konto z użytkownikiem używanym do logowania się do środowiska SAP CAL, kliknij przycisk **Przejrzyj**. 
 
1. Aby utworzyć skojarzenie między użytkownikiem a nowo utworzonym kontem SAP CAL, kliknij przycisk **Utwórz**.

   ![Skojarzenie konta użytkownika z kontem SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Pomyślnie utworzono konto SAP CAL, które może:

- Użyj modelu wdrażania usługi Resource Manager.
- Wdrażanie systemów SAP w ramach subskrypcji platformy Azure.

Teraz możesz rozpocząć wdrażanie S/4HANA w subskrypcji użytkowników na platformie Azure.

> [!NOTE]
> Przed kontynuowaniem należy określić, czy masz przydziały platformy Azure dla maszyn wirtualnych platformy Azure z serii H. Obecnie środowisko SAP CAL używa maszyn wirtualnych z serii H platformy Azure do wdrażania niektórych rozwiązań opartych na sap HANA. Twoja subskrypcja platformy Azure może nie mieć żadnych przydziałów vCPU serii H dla serii H. Jeśli tak, może być konieczne skontaktowanie się z pomocą techniczną platformy Azure, aby uzyskać przydział co najmniej 16 procesorów wirtualnych z serii H.
> 
> [!NOTE]
> Po wdrożeniu rozwiązania na platformie Azure w pliku CAL SAP może się okazać, że można wybrać tylko jeden region platformy Azure. Aby wdrożyć w regionach platformy Azure innych niż sugerowane przez środowisko CAL SAP, należy zakupić subskrypcję cal od sap. Może być również konieczne otwarcie wiadomości z SAP, aby mieć swoje konto CAL włączone do dostarczania do regionów platformy Azure innych niż te początkowo sugerowane.

### <a name="deploy-a-solution"></a>Wdrażanie rozwiązania

Zastosujmy rozwiązanie na stronie **Rozwiązania** w pliku SAP CAL. Środowisko SAP CAL ma dwie sekwencje do wdrożenia:

- Podstawowa sekwencja, która używa jednej strony do zdefiniowania systemu, który ma zostać wdrożony
- Zaawansowana sekwencja, która daje pewne możliwości wyboru rozmiarów maszyn wirtualnych 

W tym miejscu demonstrujemy podstawową ścieżkę wdrożenia.

1. Na stronie **Szczegóły konta** musisz:

    a. Wybierz konto SAP CAL. (Użyj konta skojarzonego do wdrożenia z modelem wdrażania Menedżera zasobów).

    b. Wprowadź **nazwę**wystąpienia .

    d. Wybierz **region**platformy Azure . Środowisko CAL SAP sugeruje region. Jeśli potrzebujesz innego regionu platformy Azure i nie masz subskrypcji SAP CAL, musisz zamówić subskrypcję cal za pomocą sap.

    d. Wprowadź **hasło** główne dla rozwiązania ośmiu lub dziewięciu znaków. Hasło jest używane dla administratorów różnych składników.

   ![Tryb podstawowy SAP CAL: Utwórz wystąpienie](./media/cal-s4h/s4h-pic10a.png)

1. Kliknij przycisk **Utwórz**, a w wyświetlonym oknie komunikatu kliknij przycisk **OK**.

   ![Rozmiary maszyn wirtualnych obsługiwanych przez sap CAL](./media/cal-s4h/s4h-pic10b.png)

1. W oknie dialogowym **Klucz prywatny** kliknij pozycję **Magazyn,** aby zapisać klucz prywatny w pliku SAP CAL. Aby użyć ochrony hasłem dla klucza prywatnego, kliknij przycisk **Pobierz**. 

   ![Klucz prywatny SAP CAL](./media/cal-s4h/s4h-pic10c.png)

1. Przeczytaj komunikat **Ostrzeżenie o błędzie** SAP CAL i kliknij przycisk **OK**.

   ![Ostrzeżenie SAP CAL](./media/cal-s4h/s4h-pic10d.png)

    Teraz odbywa się wdrożenie. Po pewnym czasie, w zależności od rozmiaru i złożoności rozwiązania (środowisko CAL SAP zapewnia oszacowanie), stan jest wyświetlany jako aktywny i gotowy do użycia.

1. Aby znaleźć maszyny wirtualne zebrane z innymi skojarzonymi zasobami w jednej grupie zasobów, przejdź do witryny Azure portal: 

   ![Obiekty SAP CAL wdrożone w nowym portalu](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. W portalu SAP CAL stan jest wyświetlany jako **Aktywny**. Aby połączyć się z rozwiązaniem, kliknij przycisk **Połącz**. W ramach tego rozwiązania są wdrażane różne opcje łączenia się z różnymi składnikami.

   ![Wystąpienia środowiska SAP CAL](./media/cal-s4h/active_solution.png)

1. Aby można było użyć jednej z opcji do łączenia się z wdrożonymi systemami, kliknij przycisk **Wprowadzenie**. 

   ![Łączenie się z wystąpieniem](./media/cal-s4h/connect_to_solution.png)

    Dokumentacja nazywa użytkowników dla każdej z metod łączności. Hasła dla tych użytkowników są ustawione na hasło główne zdefiniowane na początku procesu wdrażania. W dokumentacji inni użytkownicy bardziej funkcjonalni są wymienieni z hasłami, których można użyć do zalogowania się do wdrożonego systemu. 

    Jeśli na przykład używany jest preinstalowany interfejs GUI SAP na komputerze z pulpitem zdalnym z systemem Windows, system S/4 może wyglądać następująco:

   ![SM50 w preinstalowanym interfejsie SAP GUI](./media/cal-s4h/gui_sm50.png)

    Lub jeśli używasz DBACockpit, wystąpienie może wyglądać następująco:

   ![SM50 w GUI DBACockpit SAP](./media/cal-s4h/dbacockpit.png)

W ciągu kilku godzin na platformie Azure zostanie wdrożone zdrowe urządzenie SAP S/4.

Jeśli zakupiono subskrypcję sap cal, SAP w pełni obsługuje wdrożenia za pośrednictwem narzędzia SAP CAL na platformie Azure. Kolejka pomocy technicznej to BC-VCM-CAL.







