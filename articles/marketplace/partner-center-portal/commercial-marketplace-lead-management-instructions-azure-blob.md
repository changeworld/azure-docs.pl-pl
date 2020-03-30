---
title: Tabela platformy Azure w komercyjnym programie marketplace| Azure Marketplace
description: Konfigurowanie zarządzania potencjalnymi klientami dla obiektu Blob platformy Azure
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285252"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Instrukcje zarządzania potencjalnymi klientami dla obiektów Blob platformy Azure

>[!Caution]
>Opcja obiektu Blob platformy Azure do przetwarzania potencjalnych klientów z oferty portalu Marketplace została przestarzała. Jeśli masz obecnie ofertę opublikowaną z konfiguracją zarządzania potencjalnymi klientami dla obiektu Blob platformy Azure, nie otrzymujesz już potencjalnych klientów. Zaktualizuj konfigurację zarządzania potencjalnymi klientami do dowolnej z innych opcji zarządzania potencjalnymi klientami. Dowiedz się więcej o innych opcjach na [stronie docelowej zarządzania potencjalnymi klientami](./commercial-marketplace-get-customer-leads.md)."

Jeśli system zarządzania relacjami z klientami (CRM) nie jest jawnie obsługiwany w Centrum partnerów do odbierania potencjalnych klientów w portalu Azure Marketplace i AppSource, można użyć obiektu Blob platformy Azure do obsługi tych potencjalnych klientów. Następnie można wyeksportować dane i zaimportować je do systemu CRM. Instrukcje zawarte w tym artykule zapewniają proces tworzenia konta usługi Azure Storage i obiektu Blob platformy Azure na tym koncie. Ponadto można utworzyć nowy przepływ za pomocą usługi Microsoft Flow, aby wysłać powiadomienie e-mail, gdy oferta otrzyma potencjalnego klienta.


## <a name="how-to-configure-azure-blob"></a>Jak skonfigurować obiekt blob platformy Azure

1. Jeśli nie masz konta platformy Azure, możesz [utworzyć bezpłatne konto próbne](https://azure.microsoft.com/pricing/free-trial/).
1. Po aktywnym koncie platformy Azure zaloguj się do [witryny Azure portal](https://portal.azure.com).
1. W witrynie Azure portal utwórz konto magazynu przy użyciu poniższej procedury.  
    1. Wybierz **pozycję +Utwórz zasób** na lewym pasku menu.  **Okienko Nowy** (ostrze) zostanie wyświetlone po prawej stronie.
    2. Wybierz **pozycję Magazyn** w okienku **Nowy.**  Po **Featured** prawej stronie zostanie wyświetlona lista Polecane.
    3. Wybierz **konto magazynu,** aby rozpocząć tworzenie konta.  Postępuj zgodnie z instrukcjami w artykule [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Kroki tworzenia konta magazynu platformy Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Aby uzyskać więcej informacji na temat kont magazynu, wybierz [samouczek Szybki start](https://docs.microsoft.com/azure/storage/).  Aby uzyskać więcej informacji na temat cen magazynowania, zobacz [ceny magazynowania](https://azure.microsoft.com/pricing/details/storage/).

4. Poczekaj, aż konto magazynu zostanie aprowizny, proces, który zwykle trwa kilka minut.  Następnie przejdź do konta magazynu ze strony **głównej** witryny Azure portal, wybierając **pozycję Zobacz wszystkie zasoby** lub wybierając wszystkie **zasoby** z lewego paska menu nawigacji w witrynie Azure portal.

    ![Uzyskiwanie dostępu do konta magazynu platformy Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. W okienku konta magazynu wybierz pozycję **Klawisze programu Access** i skopiuj wartość *ciągu połączenia* dla klucza. Zapisz tę wartość, ponieważ jest to wartość *parametry połączenia konta magazynu,* którą należy podać w portalu publikowania, aby otrzymywać potencjalnych klientów dla oferty marketplace.

     Przykładem użądlenia połączenia jest:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Klucz magazynu platformy Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Na stronie konta magazynu wybierz pozycję **Obiekty blob**.

   ![Klucz magazynu platformy Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Po przejściu na stronę obiektów blob wybierz przycisk **+ Kontener.**

8. Wpisz **nazwę** nowego kontenera. Nazwa kontenera musi być zapisana małymi literami, zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-). Aby uzyskać dodatkowe informacje o regułach nazewnictwa kontenerów i obiektów blob, zobacz [Nazewnictwo i odwołania do kontenerów, obiektów blob i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Zapisz tę wartość, ponieważ jest to wartość *Nazwa kontenera,* którą należy podać w portalu publikowania, aby otrzymywać potencjalnych klientów dla oferty marketplace.

9. Ustaw poziom publicznego dostępu do kontenera jako **prywatny (bez dostępu anonimowego)**.

10. Wybierz przycisk **OK**, aby utworzyć kontener.

    ![Nowy kontener](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do obiektu Blob platformy Azure

Gdy będziesz gotowy skonfigurować informacje o zarządzaniu potencjalnymi klientami dla oferty w portalu publikowania, wykonaj poniższe czynności:

1. Przejdź do strony **Ustawienia oferty** dla swojej oferty.
2. Wybierz **pozycję Połącz** w sekcji Zarządzanie potencjalnymi klientami.

    ![Połącz ofertę](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. W oknie podręcznym Szczegóły połączenia wybierz pozycję Azure Blob for the Lead Destination (Szczegóły połączenia) wybierz pozycję Azure Blob for the Lead Destination (Szczegóły połączenia) w oknie podręcznym **Usługi Azure blob(NP.**

    ![Połącz szczegóły](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Podaj **nazwę kontenera** i **parametry połączenia konta magazynu,** które zostały odebrane zgodnie z tymi instrukcjami.

    * Przykład nazwy kontenera:`marketplaceleadcontainer`
    * Przykład ciągu połączenia `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![konta magazynu: Szczegóły połączenia](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Wybierz **pozycję Zapisz**.

    > [!NOTE]
    > Musisz zakończyć konfigurowanie pozostałej części oferty i opublikować ją, zanim będzie można odbierać potencjalnych klientów dla oferty.


