---
title: Rozpoczynanie pracy z usługą Data Lake Store przy użyciu witryny Azure Portal | Microsoft Docs
description: Użyj witryny Azure Portal, aby utworzyć konto usługi Data Lake Store i wykonywać podstawowe operacje w tej usłudze
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: fea324d0-ad1a-4150-81f0-8682ddb4591c
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 91ed55e5f12c10e67653bdadfca80bd072779711
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Rozpoczynanie pracy z usługą Azure Data Lake Store za pośrednictwem witryny Azure Portal
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [Program PowerShell](data-lake-store-get-started-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](data-lake-store-get-started-cli-2.0.md)
>
> 

Dowiedz się, jak za pomocą witryny Azure Portal możesz utworzyć konto usługi Azure Data Lake Store i wykonywać podstawowe operacje, takie jak tworzenie folderów, przekazywanie i pobieranie plików danych, usuwanie konta itp. Aby uzyskać więcej informacji, zobacz temat [Omówienie usługi Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-azure-data-lake-store-account"></a>Tworzenie konta usługi Azure Data Lake Store

1. Zaloguj się w nowej witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Utwórz zasób > Magazyn > Data Lake Store**.
3. W bloku **Nowa usługa Data Lake Store** podaj wartości, jak pokazano na poniższym zrzucie ekranu:
   
    ![Tworzenie nowego konta usługi Azure Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Tworzenie nowego konta usługi Azure Data Lake")
   
   * **Nazwa**. Wprowadź unikatową nazwę konta usługi Data Lake Store.
   * **Subskrypcja**. Wybierz subskrypcję, w ramach której chcesz utworzyć nowe konto usługi Data Lake Store.
   * **Grupa zasobów**. Wybierz istniejącą grupę zasobów lub użyj opcji **Utwórz nową**, aby utworzyć taką grupę. Grupa zasobów to kontener, który zawiera powiązane zasoby dla aplikacji. Aby uzyskać więcej informacji, zobacz [Grupy zasobów na platformie Azure](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Lokalizacja**. Wybierz lokalizację, w której chcesz utworzyć konto usługi Data Lake Store.
   * **Ustawienia szyfrowania**. Dostępne są trzy opcje:
     
     * **Nie włączaj szyfrowania**.
     * **Użyj kluczy zarządzanych przez usługę Data Lake Store**.  Jeśli chcesz, aby usługa Azure Data Lake Store zarządzała Twoimi kluczami szyfrowania.
     * **Użyj kluczy z własnej usługi Key Vault**. Możesz wybrać istniejącą usługę Azure Key Vault lub utworzyć nową usługę Key Vault. Aby użyć kluczy z usługi Key Vault, musisz przypisać do konta usługi Azure Data Lake Store uprawnienia dostępu do usługi Azure Key Vault. Aby uzyskać instrukcje, zobacz [Przypisywanie uprawnień do usługi Azure Key Vault](#assign-permissions-to-azure-key-vault).
       
        ![Szyfrowanie usługi Data Lake Store](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Szyfrowanie usługi Data Lake Store")
       
        Kliknij przycisk **OK** w bloku **Ustawienia szyfrowania**.

        Aby uzyskać więcej informacji, zobacz [Szyfrowanie danych w usłudze Azure Data Lake Store](./data-lake-store-encryption.md).

4. Kliknij przycisk **Utwórz**. Jeśli wybrano opcję przypięcia konta do pulpitu nawigacyjnego, nastąpi powrót do pulpitu nawigacyjnego i będzie widoczny postęp aprowizowania konta usługi Data Lake Store. Po aprowizowaniu konta usługi Data Lake Store pojawi się blok konta.

## <a name="assign-permissions-to-azure-key-vault"></a>Przypisywanie uprawnień do usługi Azure Key Vault
Jeśli używasz kluczy z usługi Azure Key Vault do skonfigurowania szyfrowania konta Data Lake Store, musisz skonfigurować dostęp między kontami usług Azure Data Lake a Azure Key Vault. W tym celu wykonaj poniższe kroki.

1. Jeśli użyto kluczy z usługi Azure Key Vault, na górze bloku konta usługi Data Lake Store zostanie wyświetlone ostrzeżenie. Kliknij ostrzeżenie, aby otworzyć obszar **Szyfrowanie**.
   
    ![Szyfrowanie usługi Data Lake Store](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Szyfrowanie usługi Data Lake Store")
2. Blok zawiera dwie opcje umożliwiające skonfigurowanie dostępu.

    ![Szyfrowanie usługi Data Lake Store](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Szyfrowanie usługi Data Lake Store")
   
   * W pierwszej opcji kliknij pozycję **Udziel uprawnień** w celu skonfigurowania dostępu. Pierwsza opcja jest włączona tylko wtedy, gdy użytkownik, który utworzył konto usługi Data Lake Store jest również administratorem usługi Azure Key Vault.
   * Druga opcja polega na uruchomieniu polecenia cmdlet programu PowerShell wyświetlonego w bloku. Musisz być właścicielem usługi Azure Key Vault lub mieć możliwość udzielania uprawnień w usłudze Azure Key Vault. Po uruchomieniu polecenia cmdlet wróć do bloku i kliknij przycisk **Włącz**, aby skonfigurować dostęp.

> [!NOTE]
> Konto usługi Data Lake Store możesz również utworzyć za pomocą szablonów usługi Azure Resource Manager. Te szablony są dostępne na stronie [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
    - Bez szyfrowania danych: [wdróż konto usługi Azure Data Lake Store bez szyfrowania danych](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
    - Z szyfrowaniem danych przy użyciu usługi Data Lake Store: [wdróż konto usługi Data Lake Store z szyfrowaniem (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
    - Z szyfrowaniem danych przy użyciu usługi Azure Key Vault: [wdróż konto usługi Data Lake Store z szyfrowaniem (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
> 
> 



## <a name="createfolder"></a>Tworzenie folderów w ramach konta usługi Azure Data Lake Store
W ramach konta usługi Data Lake Store możesz tworzyć foldery w celu zarządzania danymi i przechowywania ich.

1. Otwórz utworzone konto usługi Data Lake Store. W okienku po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie w bloku Wszystkie zasoby kliknij nazwę konta, w ramach którego chcesz utworzyć foldery. Jeśli konto jest przypięte do tablicy startowej, kliknij kafelek konta.
2. W bloku konta usługi Data Lake Store kliknij pozycję **Eksplorator danych**.
   
    ![Tworzenie folderów na koncie usługi Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Tworzenie folderów na koncie usługi Data Lake Store")
3. W bloku Eksplorator danych kliknij pozycję **Nowy folder**, wprowadź nazwę nowego folderu, a następnie kliknij przycisk **OK**.
   
    ![Tworzenie folderów na koncie usługi Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Tworzenie folderów na koncie usługi Data Lake Store")
   
    Nowo utworzony folder jest widoczny na liście w bloku **Eksplorator danych**. Foldery możesz zagnieżdżać do dowolnego poziomu.
   
    ![Tworzenie folderów na koncie usługi Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Tworzenie folderów na koncie usługi Data Lake")

## <a name="uploaddata"></a>Przekazywanie danych do konta usługi Azure Data Lake Store
Swoje dane możesz przekazać do konta usługi Azure Data Lake Store bezpośrednio na poziom główny albo do folderu utworzonego w ramach konta. 

1. W bloku **Eksplorator danych** kliknij przycisk **Przekaż**. 
2. W bloku **Przekaż pliki** przejdź do plików, które chcesz przekazać, a następnie kliknij pozycję **Dodaj wybrane pliki**. Możesz też wybrać więcej niż jeden plik do przekazania.

    ![Przekazywanie danych](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Przekazywanie danych")

Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="properties"></a>Akcje dostępne dla przechowywanych danych
Kliknij ikonę wielokropka dla pliku, a następnie w menu podręcznym kliknij akcję, którą chcesz wykonać na danych.

![Właściwości danych](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Właściwości danych") 

## <a name="secure-your-data"></a>Zabezpieczanie danych
Dane przechowywane w ramach konta usługi Azure Data Lake Store możesz zabezpieczyć, korzystając z usługi Azure Active Directory i kontroli dostępu (listy ACL). Aby uzyskać instrukcje, jak to zrobić, zobacz [Zabezpieczanie danych w usłudze Azure Data Lake Store](data-lake-store-secure-data.md).

## <a name="delete-azure-data-lake-store-account"></a>Usuwanie konta usługi Azure Data Lake Store
Aby usunąć konto usługi Azure Data Lake Store z bloku Data Lake Store, kliknij pozycję **Usuń**. W celu potwierdzenia akcji zostanie wyświetlony monit o wprowadzenie nazwy konta, które chcesz usunąć. Wprowadź nazwę konta, a następnie kliknij pozycję **Usuń**.

![Usuwanie konta usługi Data Lake](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Usuwanie konta usługi Data Lake")

## <a name="next-steps"></a>Następne kroki
* [Używanie usługi Azure Data Lake Store w zastosowaniach wymagających danych big data](data-lake-store-data-scenarios.md) 
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

