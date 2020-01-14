---
title: Jak uzyskać pomoc techniczną dotyczącą avere vFXT dla platformy Azure
description: Wyjaśnienie otwartych biletów pomocy technicznej dotyczących usługi avere vFXT for Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 27f32a922a1b063096b0ccf28a01a78d442e0271
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889282"
---
# <a name="get-help-with-your-system"></a>Uzyskaj pomoc dotyczącą systemu

Jeśli potrzebujesz pomocy dotyczącej programu avere vFXT for Azure, poniżej przedstawiono różne sposoby uzyskiwania pomocy technicznej:

* **Problem z avere vFXT** — Użyj Azure Portal, aby otworzyć bilet pomocy technicznej dla vFXT avere, zgodnie z [poniższym](#open-a-support-ticket-for-your-avere-vfxt)opisem.
* **Przydział** — Jeśli masz problem związany z przydziałem, [Poproś o zwiększenie limitu przydziału](#request-a-quota-increase)
* **Dokumentacja i przykłady** — Jeśli znajdziesz problemy z tą dokumentacją lub przykładami, przewiń w dół strony, a następnie skorzystaj z sekcji **Opinie** , aby wyszukać istniejące problemy i w razie potrzeby zastąpić nowe rozwiązanie.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Otwórz bilet pomocy technicznej dla Twojego avere vFXT

Jeśli wystąpią problemy podczas wdrażania lub korzystania z avere vFXT, poproś o pomoc za pośrednictwem Azure Portal.

Wykonaj następujące kroki, aby upewnić się, że bilet pomocy technicznej jest oznaczony przy użyciu zasobu z klastra. Tagowanie biletu pomaga nam kierować go do poprawnego zasobu pomocy technicznej.

1. W obszarze [https://portal.azure.com](https://portal.azure.com)wybierz pozycję **grupy zasobów**.

   ![zrzut ekranu przedstawiający menu Azure Portal z lewej strony z wyróżnioną "grupami zasobów"](media/avere-vfxt-ticket-rg.png)

1. Przejdź do grupy zasobów zawierającej klaster vFXT, w którym wystąpił problem, a następnie kliknij jedną z maszyn wirtualnych avere.

    ![zrzut ekranu przedstawiający panel "przegląd" Azure Portal grupy zasobów z określoną maszyną wirtualną](media/avere-vfxt-ticket-vm.png)

1. Na stronie VM przewiń w dół do końca lewego panelu, a następnie kliknij pozycję **nowe żądanie obsługi**.

    ![Zrzut ekranu strony maszyny wirtualnej Azure Portal dla maszyny wirtualnej z poprzedniego zrzutu ekranu. Menu po lewej stronie jest przewijane do dołu i "nowe żądanie obsługi" jest zakreślone.](media/avere-vfxt-ticket-request.png)

1. Na stronie jedno żądanie obsługi kliknij pozycję **wszystkie usługi** i w obszarze **Magazyn** wybierz opcję **avere vFXT**.

    ![zrzut ekranu przedstawiający nowy ekran żądania obsługi w Azure Portal z nagłówkiem "podstawowe" i okręgiem dookoła elementu "usługa". Wybrano przycisk "wszystkie usługi", a pole menu rozwijanego ma wartość "avere vFXT"](media/avere-vfxt-ticket-service.png)

1. Na stronie dwie wybierz typ problemu i kategorię, które najlepiej pasują do Twojego problemu. Dodaj krótki tytuł i opis, który zawiera czas wystąpienia problemu.

   ![zrzut ekranu przedstawiający nowy ekran żądania obsługi z nagłówkiem "problem" zawierającym wiele pól, które należy wypełnić](media/avere-vfxt-ticket-problem.png)

1. Na stronie trzy wprowadź informacje kontaktowe, a następnie kliknij przycisk **Utwórz**. Potwierdzenie i numer biletu zostaną wysłane na Twój adres e-mail, a członek personelu pomocy technicznej skontaktuje się z Tobą.

## <a name="request-a-quota-increase"></a>Poproś o zwiększenie limitu przydziału

Zapoznaj się z [limitem przydziału dla klastra vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) , aby dowiedzieć się, jakie składniki są potrzebne do wdrożenia avere VFXT dla platformy Azure. Można [zażądać zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) z Azure Portal.
