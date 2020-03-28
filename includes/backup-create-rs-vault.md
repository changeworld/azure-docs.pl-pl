---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: 6167774171affda7e5469d5852a79657a6da700d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262329"
---
## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn usług odzyskiwania to jednostka magazynu, która przechowuje punkty odzyskiwania utworzone w czasie. Zawiera również zasady tworzenia kopii zapasowych, które są skojarzone z elementami chronionymi.

Aby utworzyć magazyn usług odzyskiwania, wykonaj następujące kroki.

1. Zaloguj się do subskrypcji w witrynie [Azure Portal](https://portal.azure.com/).

1. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**.

    ![Wybieranie pozycji Wszystkie usługi](./media/backup-create-rs-vault/click-all-services.png)

1. W oknie dialogowym **Wszystkie usługi** wprowadź frazę *Recovery Services*. Lista filtrów zasobów zgodnie z wejściem. Na liście zasobów wybierz pozycję **Magazyny usług odzyskiwania**.

    ![Wprowadzanie i wybieranie pozycji Magazyny usługi Recovery Services](./media/backup-create-rs-vault/all-services.png)

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.

1. Na **pulpicie** nawigacyjnym magazynów usług odzyskiwania wybierz pozycję **Dodaj**.

    ![Dodawanie magazynu usług odzyskiwania](./media/backup-create-rs-vault/add-button-create-vault.png)

    Zostanie otwarte okno dialogowe **Magazyn usług odzyskiwania.** Podaj wartości **nazwy,** **subskrypcji,** **grupy zasobów**i **lokalizacji**.

    ![Konfigurowanie magazynu usług odzyskiwania](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nazwa**: Wprowadź przyjazną nazwę, aby zidentyfikować przechowalnię. Nazwa musi być unikatowa dla subskrypcji platformy Azure. Określ nazwę, która ma co najmniej 2, ale nie więcej niż 50 znaków. Nazwa musi zaczynać się od litery i składać się tylko z liter, cyfr i łączników.
   - **Subskrypcja**: Wybierz subskrypcję do użycia. Jeśli jesteś członkiem tylko jednej subskrypcji, zobaczysz tę nazwę. Jeśli nie masz pewności, której subskrypcji użyć, użyj domyślnej (sugerowanej) subskrypcji. Istnieje wiele opcji tylko wtedy, gdy konto służbowe jest skojarzone z więcej niż jedną subskrypcją platformy Azure.
   - **Grupa zasobów:** Użyj istniejącej grupy zasobów lub utwórz nową. Aby wyświetlić listę dostępnych grup zasobów w ramach subskrypcji, wybierz pozycję **Użyj istniejącego**, a następnie wybierz zasób z listy rozwijanej. Aby utworzyć nową grupę zasobów, wybierz pozycję **Utwórz nowy** i wprowadź jej nazwę. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Lokalizacja**: Wybierz region geograficzny przechowalni. Aby utworzyć magazyn w celu ochrony maszyn wirtualnych, magazyn *musi* znajdować się w tym samym regionie co maszyny wirtualne.

      > [!IMPORTANT]
      > Jeśli nie masz pewności co do lokalizacji maszyny Wirtualnej, zamknij okno dialogowe. Przejdź do listy maszyn wirtualnych w portalu. Jeśli masz maszyny wirtualne w kilku regionach, utwórz magazyn usług odzyskiwania w każdym regionie. Utwórz przechowalnię w pierwszej lokalizacji, zanim utworzysz przechowalnię dla innej lokalizacji. Nie ma potrzeby określania kont magazynu do przechowywania danych kopii zapasowej. Magazyn usług odzyskiwania i usługa Azure Backup obsługują to automatycznie.
      >
      >

1. Gdy będziesz gotowy do utworzenia magazynu usług odzyskiwania, wybierz pozycję **Utwórz**.

    ![Tworzenie magazynu usług odzyskiwania](./media/backup-create-rs-vault/click-create-button.png)

    Utworzenie magazynu usług odzyskiwania może chwilę potrwać. Monitorowanie powiadomień o stanie w obszarze **Powiadomienia** w prawym górnym rogu portalu. Po utworzeniu przechowalni jest ona widoczna na liście magazynów usług odzyskiwania. Jeśli nie widzisz przechowalni, wybierz pozycję **Odśwież**.

     ![Odświeżanie listy magazynów kopii zapasowych](./media/backup-create-rs-vault/refresh-button.png)
