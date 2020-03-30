---
title: Jak uzyskać pomoc techniczną dla Avere vFXT for Azure
description: Wyjaśnienie otwierania biletów pomocy technicznej dotyczących usługi Avere vFXT for Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: b3c604003f78a150067875fb2064ab5eca0600db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252560"
---
# <a name="get-help-with-your-system"></a>Uzyskaj pomoc dotyczącą systemu

Aby uzyskać pomoc dotyczącą systemu Avere vFXT for Azure, oto sposoby uzyskania pomocy technicznej:

* **Problem avere vFXT** — użyj witryny Azure portal, aby otworzyć bilet pomocy technicznej dla usługi Avere vFXT, jak opisano [poniżej.](#open-a-support-ticket-for-your-avere-vfxt)
* **Przydział** — jeśli masz problem związany z przydziałem, [poproś o zwiększenie przydziału](#request-a-quota-increase)
* **Dokumentacja i przykłady** — jeśli znajdziesz problemy z tą dokumentacją lub przykładami, przewiń do dołu strony z problemem i użyj sekcji **Opinie,** aby wyszukać istniejące problemy i w razie potrzeby złożyć nowy.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Otwórz bilet pomocy technicznej dla urządzenia Avere vFXT

Jeśli wystąpią problemy podczas wdrażania lub korzystania z Avere vFXT, poproś o pomoc za pośrednictwem witryny Azure portal.

Wykonaj następujące kroki, aby upewnić się, że bilet pomocy technicznej jest oznaczony zasobem z klastra. Oznaczanie biletu pomaga nam przekierować go do odpowiedniego zasobu pomocy technicznej.

1. Z [https://portal.azure.com](https://portal.azure.com)wybierz pozycję **Grupy zasobów**. Przejdź do grupy zasobów zawierającej klaster vFXT, w której wystąpił problem, i kliknij jedną z maszyn wirtualnych klastra Avere.

    ![zrzut ekranu przedstawiający panel "przegląd" grupy zasobów witryny Azure portal z zakreśloną maszyną wirtualną](media/avere-vfxt-ticket-vm.png)

1. Na stronie maszyny Wirtualnej przewiń w dół do dołu lewego panelu i kliknij pozycję **Nowe żądanie pomocy technicznej**.

    ![Zrzut ekranu przedstawiający stronę maszyny Wirtualnej witryny Azure portal dla maszyny Wirtualnej z poprzedniego zrzutu ekranu. Lewe menu jest przewijane do dołu i kółko jest "Nowe żądanie obsługi".](media/avere-vfxt-ticket-request.png)

1. Na pierwszej stronie żądania pomocy technicznej wybierz typ problemu i upewnij się, że jest wybrana poprawna subskrypcja.

   W obszarze **Usługa**kliknij **pozycję Wszystkie usługi** i poszukaj w obszarze **Magazyn,** aby wybrać **opcję Avere vFXT**.

   Dodaj krótkie podsumowanie i wybierz typ problemu.

    ![zrzut ekranu nowego ekranu żądania pomocy technicznej w witrynie Azure portal. Zostanie wybrana karta Podstawy. Elementy ekranu obejmują typ problemu, subskrypcja, usługa, podsumowanie i typ problemu.](media/ticket-basics.png)

   Kliknij **przycisk Dalej,** aby kontynuować.

1. Druga strona formularza pomocy technicznej zawiera sugestie dotyczące rozwiązania problemu na podstawie opisu podsumowania. Kliknij przycisk **Dalej** u dołu, jeśli nadal musisz utworzyć bilet pomocy technicznej.

   ![zrzut ekranu nowego ekranu żądania pomocy technicznej z wybraną kartą Rozwiązania. Pole tekstowe w środku ma tytuł "Zalecane rozwiązanie" i wyjaśnia możliwe środki zaradcze.](media/ticket-solutions.png)

1. Na trzeciej stronie podaj szczegóły — obejmuje to informacje o klastrze, czasie wystąpienia problemu, ważności i sposobie kontaktowania się z Tobą. Wypełnij informacje i kliknij przycisk **Dalej** u dołu.

   ![zrzut ekranu nowego ekranu żądania pomocy technicznej z wybraną kartą Szczegóły. Pola informacyjne są podzielone na kategorie "Szczegóły problemu", "Metoda pomocy technicznej" i "Informacje kontaktowe".](media/ticket-details.png)

1. Przejrzyj informacje na ostatniej stronie i kliknij przycisk **Utwórz**. Na Twój adres e-mail zostanie wysłany numer potwierdzenia i biletu, a pracownik pomocy technicznej skontaktuje się z Tobą.

## <a name="request-a-quota-increase"></a>Poproś o zwiększenie kwoty

Przeczytaj [przydział dla klastra vFXT,](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) aby dowiedzieć się, jakie składniki są potrzebne do wdrożenia avere vFXT dla platformy Azure. Możesz [zażądać zwiększenia przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) z witryny Azure portal.
