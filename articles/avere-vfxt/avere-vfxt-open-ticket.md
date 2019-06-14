---
title: Jak uzyskać pomoc techniczną dla Avere vFXT dla platformy Azure
description: Wyjaśnienie otwierania biletów pomocy technicznej o Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d621511cbb6983f8ad57ea8305823039475f40d0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409691"
---
# <a name="get-help-with-your-system"></a>Uzyskaj pomoc dotyczącą systemu

Jeśli potrzebujesz pomocy przy użyciu usługi vFXT Avere dla platformy Azure, w tym miejscu są różne sposoby, aby uzyskać pomoc techniczną:

* **Problem vFXT Avere** — użyj witryny Azure portal, aby otworzyć bilet pomocy technicznej dla usługi vFXT Avere zgodnie z opisem [poniżej](#open-a-support-ticket-for-your-avere-vfxt).
* **Limit przydziału** — Jeśli masz problemy z dotyczące limitu przydziału [zażądać zwiększenia limitu przydziału](#request-a-quota-increase)
* **Dokumentacja i przykłady** — Jeśli okaże się problemów z tej dokumentacji lub przykładów, przewiń w dół strony o problemie i użyj **opinii** sekcji, aby wyszukać istniejące problemy i nowego przypadku jeden plik wymagane.  

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Otwórz bilet pomocy technicznej dla usługi vFXT Avere

Jeśli wystąpią problemy podczas wdrażania lub za pomocą Avere vFXT prosić o pomoc za pośrednictwem witryny Azure portal.  

Wykonaj następujące kroki, aby upewnić się, że biletu pomocy technicznej jest oznaczane zasobu z klastra. Znakowanie--ticket pomoże nam skierować go do zasobu poprawną pomocy technicznej. 

1. Z [ https://portal.azure.com ](https://portal.azure.com), wybierz opcję **grup zasobów**.

   ![Zrzut ekranu przedstawiający portal lewe menu platformy Azure za pomocą "Grupy zasobów" w kółkach](media/avere-vfxt-ticket-rg.png)

1. Przejdź do grupy zasobów, który zawiera klastra vFXT, w którym wystąpił problem, a następnie kliknij jedną z maszyn wirtualnych Avere.

    ![Zrzut ekranu przedstawiający panelu grupy "Przegląd" zasobów portalu platformy Azure przy użyciu określonej maszyny Wirtualnej w kółkach](media/avere-vfxt-ticket-vm.png)

1. Na stronie maszyny Wirtualnej, przewiń w dół do dolnej części panelu po lewej stronie, a następnie kliknij przycisk **nowe żądanie obsługi**.

    ![Zrzut ekranu przedstawiający stronę maszyny Wirtualnej portalu Azure dla maszyny Wirtualnej z poprzedniego zrzutu ekranu. W menu po lewej stronie jest przewijane do dołu i "Nowe żądanie obsługi" jest zaznaczona kółkiem.](media/avere-vfxt-ticket-request.png)

1. Na pierwszej stronie żądania pomocy technicznej, kliknij przycisk **wszystkich usług** i poszukaj w obszarze **magazynu** wybrać **Avere vFXT**.

    ![Zrzut ekranu przedstawiający nowy ekran żądania pomocy technicznej w witrynie Azure portal z nagłówkiem "Podstawowe" i kółko wokół elementu "Usługa". Przycisk "Wszystkie usługi" jest zaznaczone, a pole listy rozwijanej ma wartość "Avere vFXT"](media/avere-vfxt-ticket-service.png)

1. Na drugiej strony wybierz typ problemu i kategorię, która najlepiej odpowiada problemu. Dodaj krótki tytuł i opis, który obejmuje czas wystąpienia problemu. 

   ![Zrzut ekranu przedstawiający nowy ekran żądania pomocy technicznej z nagłówkiem "Problem", który zawiera wiele pól, które muszą zostać wykonane](media/avere-vfxt-ticket-problem.png)

1. Na stronie trzy, podaj swoje informacje kontaktowe i kliknij przycisk **Utwórz**. Numer biletu i potwierdzania, będą wysyłane na adres e-mail, a pracownik pomocy technicznej skontaktuje się z Tobą.

## <a name="request-a-quota-increase"></a>Zażądać zwiększenia limitu przydziału

Odczyt [limitu przydziału dla klastra vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) Aby dowiedzieć się, jakie składniki są wymagane do wdrożenia Avere vFXT dla platformy Azure. Możesz [zażądać zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) w witrynie Azure portal.