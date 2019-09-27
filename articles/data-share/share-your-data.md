---
title: 'Samouczek: Udostępnianie poza swoją organizacji — wersja zapoznawcza usługi Azure Data Share'
description: Samouczek — udostępnianie danych klientom i partnerom przy użyciu wersji zapoznawczej usługi Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: f7df46a6a6f149ef0228fda8c967469a25dc3d50
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327416"
---
# <a name="tutorial-share-your-data-using-azure-data-share-preview"></a>Samouczek: Udostępnianie danych przy użyciu wersji zapoznawczej usługi Azure Data Share

W tym samouczku dowiesz się, jak skonfigurować nowy udział danych platformy Azure i zacząć udostępniać dane klientom i partnerom spoza organizacji platformy Azure. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz udział danych.
> * Dodaj zestawy danych do swojego udziału.
> * Włącz harmonogram synchronizacji dla udziału danych. 
> * Dodaj adresatów do udziału danych. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Konto usługi Azure Storage: Jeśli jeszcze tego nie masz, możesz utworzyć [konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Uprawnienie do dodawania przypisywania roli do konta magazynu, które jest obecne w uprawnieniach *Microsoft. Autoryzacja/Przypisywanie ról/zapis* . To uprawnienie istnieje w roli właściciela. 
* Adresaci adresu e-mail logowania platformy Azure (przy użyciu aliasu poczty e-mail nie będą zadziałały).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Tworzenie konta udziału danych

Utwórz zasób udziału danych platformy Azure w grupie zasobów platformy Azure.

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu.

1. Wyszukaj *udział danych*.

1. Wybierz pozycję udział danych (wersja zapoznawcza) i wybierz pozycję **Utwórz**.

1. Wypełnij podstawowe szczegóły zasobu udziału danych platformy Azure, korzystając z poniższych informacji. 

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Name | *datashareacount* | Określ nazwę konta udziału danych. |
    | Subscription | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla konta udziału danych.|
    | Resource group | *test-resource-group* | Użyj istniejącej grupy zasobów lub utwórz nową. |
    | Location | *Wschodnie stany USA 2* | Wybierz region dla konta udziału danych.
    | | |

1. Wybierz pozycję **Utwórz** , aby zainicjować obsługę administracyjną konta udziału danych. Inicjowanie obsługi nowego konta udziału danych zwykle trwa około 2 minuty. 

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

## <a name="create-a-data-share"></a>Tworzenie udziału danych

1. Przejdź do strony Przegląd udostępniania danych.

    ![Udostępnianie danych]do(./media/share-receive-data.png "udostępniania danych") 

1. Wybierz pozycję **Rozpocznij udostępnianie danych**.

1. Wybierz pozycję **Utwórz**.   

1. Wypełnij szczegóły dotyczące udziału danych. Określ nazwę, opis zawartości udziału i warunki użytkowania (opcjonalnie). 

    ![EnterShareDetails](./media/enter-share-details.png "Wprowadź szczegóły udostępniania") 

1. Wybierz pozycję **Kontynuuj**

1. Aby dodać zbiory danych do swojego udziału, wybierz pozycję **Dodaj zestawy**. 

    (./media/datasets.png "Zestawy danych") ![DataSets]

1. Wybierz typ zestawu danych, który chcesz dodać. 

    ![Adddatasets](./media/add-datasets.png "Add DataSets")    

1. Przejdź do obiektu, który chcesz udostępnić, i wybierz pozycję "Dodaj zestawy danych". 

    ![SelectDatasets](./media/select-datasets.png "Wybierz zestawy danych")    

1. Na karcie adresaci wprowadź adres e-mail odbiorcy danych, wybierając pozycję "+ Dodaj odbiorcę". 

    ![](./media/add-recipient.png "Dodawanie adresatów dla odbiorców") 

1. Wybierz pozycję **Kontynuuj**

1. Jeśli chcesz, aby odbiorca danych mógł uzyskać aktualizacje przyrostowe danych, Włącz harmonogram migawek. 

    ![EnableSnapshots](./media/enable-snapshots.png "Włącz migawki") 

1. Wybierz czas rozpoczęcia i interwał cyklu. 

1. Wybierz pozycję **Kontynuuj**

1. Na karcie Recenzja + tworzenie przejrzyj zawartość pakietu, ustawienia, adresatów i ustawienia synchronizacji. Wybierz pozycję **Utwórz**

Udział danych platformy Azure został utworzony, a odbiorca Twojego udziału danych jest teraz gotowy do zaakceptowania Twojego zaproszenia. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia udziału danych platformy Azure i zapraszania adresatów. Aby dowiedzieć się, jak odbiorca danych może zaakceptować i odebrać udział danych, przejdź do samouczka [akceptowanie i odbieranie danych](subscribe-to-data-share.md) . 
