---
title: Wdrażanie dedykowanych hostów platformy Azure przy użyciu Azure Portal
description: Wdróż maszyny wirtualne na dedykowanych hostach przy użyciu Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 5af09cf7ef6c811a239a64c5c6349c3625316177
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970750"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Wdrażanie maszyn wirtualnych na dedykowanych hostach przy użyciu portalu

W tym artykule opisano sposób tworzenia [dedykowanego hosta](dedicated-hosts.md) platformy Azure do hostowania maszyn wirtualnych. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu okna witryny Azure Portal.
1. W polu wyszukiwania nad listą zasobów Microsoft Azure Marketplace wpisz **Ubuntu Server 16.04 LTS by Canonical**, wybierz odpowiednią pozycję i kliknij **Utwórz**.
1. Na karcie **podstawy** w obszarze **szczegóły projektu**upewnij się, że wybrano poprawną subskrypcję, a następnie wybierz pozycję *myDedicatedHostsRG* jako **grupę zasobów**. 
1. W obszarze **Szczegóły wystąpienia** wpisz *myVM* w polu **Nazwa maszyny wirtualnej** i wybierz *Wschodnie stany USA* w polu **Lokalizacja** .
1. W obszarze **Opcje dostępności** wybierz pozycję **strefa dostępności**, a następnie z listy rozwijanej wybierz pozycję *1* .
1. Dla rozmiaru wybierz pozycję **Zmień rozmiar**. Na liście dostępnych rozmiarów wybierz jedną z serii Esv3, na przykład **standardowa E2s v3**. Może być konieczne wyczyszczenie filtru, aby wyświetlić wszystkie dostępne rozmiary.
1. W obszarze **Konto administratora** wybierz opcję **klucz publiczny SSH**, wpisz nazwę użytkownika, a następnie wklej swój klucz publiczny w polu tekstowym. Usuń wszystkie wiodące i końcowe białe znaki z klucza publicznego.

    ![Konto administratora](./media/quick-create-portal/administrator-account.png)

1. W obszarze **reguły portów ruchu przychodzącego** > **publicznych portów przychodzących**wybierz opcję **Zezwalaj na wybrane porty** , a następnie wybierz pozycję **SSH (22)** z listy rozwijanej. 
1. W górnej części strony wybierz kartę **Zaawansowane** i w sekcji **host** wybierz pozycję Moja *host* dla **grupy hostów** *i hosta* dla **hosta**. 
    ![wybierz grupę hostów i hosta](./media/dedicated-hosts-portal/advanced.png)
1. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **Przejrzyj + utwórz** znajdujący się u dołu strony.
1. Gdy zobaczysz komunikat, że Walidacja zakończyła się pomyślnie, wybierz pozycję **Utwórz**.

Wdrożenie maszyny wirtualnej potrwa kilka minut.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz Omówienie [dedykowanych hostów](dedicated-hosts.md) .

- Istnieje przykładowy szablon, który znajduje się w [tym miejscu](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), który używa stref i domen błędów w celu uzyskania maksymalnej odporności w regionie.

- Możesz również wdrożyć dedykowanego hosta za pomocą [interfejsu wiersza polecenia platformy Azure](dedicated-hosts-cli.md).



