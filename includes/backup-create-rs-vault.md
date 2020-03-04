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
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262329"
---
## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu Usług odzyskiwania

Magazyn Recovery Services jest jednostką magazynową, która przechowuje punkty odzyskiwania utworzone w czasie. Zawiera również zasady tworzenia kopii zapasowych, które są skojarzone z elementami chronionymi.

Aby utworzyć magazyn Recovery Services, wykonaj następujące kroki.

1. Zaloguj się do subskrypcji w witrynie [Azure Portal](https://portal.azure.com/).

1. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**.

    ![Wybieranie pozycji Wszystkie usługi](./media/backup-create-rs-vault/click-all-services.png)

1. W oknie dialogowym **Wszystkie usługi** wprowadź frazę *Recovery Services*. Lista filtrów zasobów zgodnie z danymi wejściowymi. Na liście zasobów wybierz pozycję **magazyny Recovery Services**.

    ![Wprowadzanie i wybieranie pozycji Magazyny usługi Recovery Services](./media/backup-create-rs-vault/all-services.png)

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.

1. Na pulpicie nawigacyjnym **Recovery Services magazynów** wybierz pozycję **Dodaj**.

    ![Dodawanie magazynu Recovery Services](./media/backup-create-rs-vault/add-button-create-vault.png)

    Zostanie otwarte okno dialogowe **magazyn Recovery Services** . Podaj wartości dla **nazwy**, **subskrypcji**, **grupy zasobów**i **lokalizacji**.

    ![Konfigurowanie magazynu Recovery Services](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nazwa**: wprowadź przyjazną nazwę identyfikującą magazyn. Nazwa musi być unikatowa dla subskrypcji platformy Azure. Określ nazwę, która ma co najmniej 2, ale nie więcej niż 50 znaków. Nazwa musi rozpoczynać się od litery i zawierać tylko litery, cyfry i łączniki.
   - **Subskrypcja**: wybierz subskrypcję do użycia. Jeśli jesteś członkiem tylko jednej subskrypcji, zobaczysz tę nazwę. Jeśli nie masz pewności, której subskrypcji użyć, Użyj domyślnej (sugerowanej) subskrypcji. Istnieje wiele opcji, które są dostępne tylko wtedy, gdy konto służbowe jest skojarzone z więcej niż jedną subskrypcją platformy Azure.
   - **Grupa zasobów**: Użyj istniejącej grupy zasobów lub Utwórz nową. Aby wyświetlić listę dostępnych grup zasobów w ramach subskrypcji, wybierz pozycję **Użyj istniejącej**, a następnie wybierz zasób z listy rozwijanej. Aby utworzyć nową grupę zasobów, wybierz pozycję **Utwórz nową** i wprowadź nazwę. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Lokalizacja**: Wybierz region geograficzny magazynu. Aby utworzyć magazyn do ochrony maszyn wirtualnych, magazyn *musi* znajdować się w tym samym regionie co maszyny wirtualne.

      > [!IMPORTANT]
      > Jeśli nie masz pewności co do lokalizacji maszyny wirtualnej, Zamknij okno dialogowe. Przejdź do listy maszyn wirtualnych w portalu. Jeśli masz maszyny wirtualne w kilku regionach, Utwórz magazyn Recovery Services w każdym regionie. Utwórz magazyn w pierwszej lokalizacji, zanim utworzysz magazyn dla innej lokalizacji. Nie ma potrzeby określania kont magazynu do przechowywania danych kopii zapasowej. Magazyn Recovery Services i Azure Backup obsłużyć automatyczne.
      >
      >

1. Gdy wszystko będzie gotowe do utworzenia magazynu Recovery Services, wybierz pozycję **Utwórz**.

    ![Tworzenie magazynu Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    Utworzenie magazynu Recovery Services może chwilę potrwać. Monitoruj powiadomienia o stanie w obszarze **powiadomień** w prawym górnym rogu portalu. Po utworzeniu magazynu będzie on widoczny na liście magazynów Recovery Services. Jeśli Twój magazyn nie jest widoczny, wybierz pozycję **Odśwież**.

     ![Odświeżanie listy magazynów kopii zapasowych](./media/backup-create-rs-vault/refresh-button.png)
