---
title: Samouczek — udostępniać dane z klientami i partnerami przy użyciu udziału danych platformy Azure w wersji zapoznawczej
description: Samouczek — udostępniać dane z klientami i partnerami przy użyciu udziału danych platformy Azure w wersji zapoznawczej
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: ebd60607f9a24074e0fa985973dfc35674f0d66c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788227"
---
# <a name="tutorial-share-your-data-using-azure-data-share-preview"></a>Samouczek: Udostępnianie danych przy użyciu udziału danych platformy Azure w wersji zapoznawczej

W tym samouczku dowiesz się, jak skonfigurować nowego udziału danych platformy Azure i Rozpocznij udostępnianie Twoje dane z klientami i partnerami spoza organizacji platformy Azure. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz udział danych.
> * Dodaj zestawy danych do udziału danych.
> * Włącz harmonogram synchronizacji dla udziału danych. 
> * Dodaj adresatów, do udziału danych. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Konto usługi Azure Storage: Jeśli nie masz jeszcze jeden, możesz utworzyć [konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Utwórz konto udostępniania danych

Utwórz zasób udziału danych platformy Azure w grupie zasobów platformy Azure.

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu.

1. Wyszukaj *udziału danych*.

1. Wybierz udział danych (wersja zapoznawcza) i wybierz pozycję **Utwórz**.

1. Wprowadź podstawowe szczegóły zasobu udziału danych platformy Azure z poniższymi informacjami. 

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Name (Nazwa) | *datashareacount* | Określ nazwę dla swojego konta udziału danych. |
    | Subscription | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, którego chcesz używać dla swojego konta udziału danych.|
    | Resource group | *test-resource-group* | Użyj istniejącej grupy zasobów lub utwórz nową. |
    | Location | *East US 2* | Wybierz region dla swojego konta udziału danych.
    | | |

1. Wybierz **Utwórz** aprowizację swojego konta udziału danych. Aprowizacja nowego konta udziału danych zazwyczaj zajmuje około 2 minuty lub mniej. 

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

## <a name="create-a-data-share"></a>Utwórz udział danych

1. Przejdź do strony Przegląd udostępniania danych.

    ![Udostępnianie danych](./media/share-receive-data.png "udostępniać swoje dane") 

1. Wybierz **Rozpocznij udostępnianie Twoje dane**.

1. Wybierz pozycję **Utwórz**.   

1. Wypełnij szczegóły dla udziału danych. Określ nazwę, opis zawartości udziału i warunki użytkowania (opcjonalnie). 

    ![EnterShareDetails](./media/enter-share-details.png "wprowadź szczegóły") 

1. Wybierz **kontynuować**

1. Aby dodać zestawy danych do udziału danych, wybierz **dodać zestawy danych**. 

    ![Zestawy danych](./media/datasets.png "zestawów danych")

1. Wybierz typ zestawu danych, który chcesz dodać. 

    ![AddDatasets](./media/add-datasets.png "dodać zestawy danych")    

1. Przejdź do obiektu, który chcesz udostępnić, a następnie wybierz pozycję "Dodaj zestawy danych". 

    ![SelectDatasets](./media/select-datasets.png "Wybierz zestawy danych")    

1. Na karcie adresatów wprowadź adresy e-mail konsumentów danych, wybierając pozycję "+ Dodaj adresata". 

    ![AddRecipients](./media/add-recipient.png "Dodaj adresatów") 

1. Wybierz **kontynuować**

1. Jeśli chcesz usługi konsumenta danych, aby można było uzyskać aktualizacje przyrostowe dane, należy włączyć harmonogramu migawek. 

    ![EnableSnapshots](./media/enable-snapshots.png "Włącz migawki") 

1. Wybierz interwał czasu i ponownego uruchamiania. 

1. Wybierz **kontynuować**

1. W przeglądzie + Utwórz kartę, przejrzyj zawartość pakietu, ustawienia, adresaci i ustawień synchronizacji. Wybierz pozycję **Utwórz**

Udziału danych platformy Azure została utworzona, a odbiorca udziału danych jest teraz gotowy umożliwiający zaakceptowanie zaproszenia. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku możesz dzięki modelom uczenia sposobu tworzenia udziału danych platformy Azure i Zaproś adresatów. Aby dowiedzieć się, jak konsumenta danych może akceptować i otrzymywać udziału danych, w dalszym ciągu [akceptować i odbierać dane](subscribe-to-data-share.md) samouczka. 