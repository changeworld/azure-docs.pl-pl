---
title: Ustawianie uprawnień dla usługi Data Lake Storage Gen2 za pomocą Eksploratora usługi Azure Storage
description: Z tego artykułu z instrukcjami dowiesz się, jak za pomocą Eksploratora usługi Azure Storage skonfigurować uprawnienia do plików i katalogów na koncie magazynu obsługującego usługę Azure Data Lake Storage Gen2.
services: storage
author: roygara
ms.custom: mvc
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: f2569b29ab6124f1cfa22fa745d45082c213a6be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627901"
---
# <a name="set-file-and-directory-level-permissions-using-azure-storage-explorer-with-azure-data-lake-storage-gen2"></a>Ustawianie uprawnień na poziomie plików i katalogów przy użyciu Eksploratora usługi Azure Storage w usłudze Azure Data Lake Storage Gen2

Pliki przechowywane w usłudze Azure Data Lake Storage Gen2 obsługują uprawnienia szczegółowe i zarządzanie listą kontroli dostępu (ACL). Uprawnienia szczegółowe w połączeniu z zarządzaniem listą ACL umożliwiają zarządzanie dostępem do danych na bardzo szczegółowym poziomie.

Ten artykuł zawiera informacje dotyczące wykonywania następujących czynności za pomocą Eksploratora usługi Azure Storage:

> [!div class="checklist"]
> * Ustawianie uprawnień na poziomie pliku
> * Ustawianie uprawnień na poziomie katalogu
> * Dodawanie użytkowników lub grup do listy kontroli dostępu

## <a name="prerequisites"></a>Wymagania wstępne

Aby jak najlepiej przedstawić ten proces, wymagane jest ukończenie [przewodnika Szybki start dotyczącego Eksploratora usługi Azure Storage](data-lake-storage-Explorer.md). Gwarantuje to, że konto magazynu będzie najbardziej odpowiedni stan (systemu plików utworzony i danymi przekazanymi do niego).

## <a name="managing-access"></a>Zarządzanie dostępem

Możesz ustawić uprawnienia w katalogu głównym systemu plików. Aby to zrobić, użytkownik musi być zalogowany do Eksploratora usługi Azure Storage na swoje indywidualne konto z uprawnieniami, aby to zrobić (w przeciwieństwie do przy użyciu parametrów połączenia). Kliknij prawym przyciskiem myszy systemu plików, a następnie wybierz pozycję **Zarządzaj uprawnieniami**, przełączanie się **Manage Permission** okno dialogowe.

![Eksplorator usługi Microsoft Azure Storage — zarządzanie dostępem do katalogów](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

Okno dialogowe **Zarządzanie uprawnieniami** służy do zarządzania uprawnieniami właściciela i grupy właściciela. Umożliwia również dodawanie do listy kontroli dostępu nowych użytkowników i grup, dla których można następnie zarządzać uprawnieniami.

Aby dodać do listy kontroli dostępu nowego użytkownika lub nową grupę, wybierz pole **Dodaj użytkownika lub grupę**.

Wprowadź odpowiedni wpis usługi Azure Active Directory (AAD), który chcesz dodać do listy, a następnie wybierz pozycję **Dodaj**.

Użytkownika lub grupę będzie teraz widać w polu **Użytkownicy i grupy:**, co umożliwi rozpoczęcie zarządzania ich uprawnieniami.

> [!NOTE]
> Najlepsze rozwiązanie i zalecenie jest takie, aby utworzyć grupę zabezpieczeń w usłudze AAD i obsługiwać uprawnienia dla grupy, a nie dla poszczególnych użytkowników. Szczegółowe informacje na temat tego zalecenia oraz inne najlepsze rozwiązania można znaleźć w [najlepszych rozwiązaniach dotyczących usługi Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Istnieją dwie kategorie uprawnień, które można przypisać: listy ACL dostępu i domyślne listy ACL.

* **Dostęp**: Listy ACL dostępu kontrolują dostęp do obiektu. Pliki i katalogi mają osobne listy ACL dostępu.

* **Domyślne**: Szablon list ACL skojarzonych z katalogiem, który określa listy ACL dostępu dla wszystkich elementów podrzędnych, które zostały utworzone w tym katalogu. Pliki nie mają domyślnych list ACL.

Obie te kategorie obejmują trzy uprawnienia, które można przypisać do plików lub katalogów: **Odczyt**, **Zapis** i **Wykonywanie**.

>[!NOTE]
> Dokonanie wyboru w tym miejscu nie spowoduje ustawienie uprawnień dla wszystkich elementów znajdujących się obecnie w katalogu. Jeśli dany plik już istnieje, należy przejść do poszczególnych elementów i ustawić uprawnienia ręcznie.

Można zarządzać uprawnieniami dla poszczególnych katalogów, a także dla pojedynczych plików, co umożliwia szczegółową kontrolę dostępu. Proces zarządzania uprawnieniami dla plików i katalogów jest taki sam, jak opisano powyżej. Kliknij prawym przyciskiem myszy plik lub katalog, dla którego chcesz zarządzać uprawnieniami, i wykonaj ten sam proces.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule z instrukcjami przedstawiono sposób ustawiania uprawnień dla plików i katalogów przy użyciu **Eksploratora usługi Azure Storage**. Aby dowiedzieć się więcej o listach ACL, w tym o domyślnych listach ACL, listach ACL dostępu i odpowiadających im uprawnieniach, przejdź do naszego koncepcyjnego artykułu na ten temat.

> [!div class="nextstepaction"]
> [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
