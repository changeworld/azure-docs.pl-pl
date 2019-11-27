---
title: 'Eksplorator usługi Azure Storage: zarządzanie dostępem w programie Azure Data Lake Storage Gen2'
description: Z tego artykułu z instrukcjami dowiesz się, jak za pomocą Eksploratora usługi Azure Storage skonfigurować uprawnienia do plików i katalogów na koncie magazynu obsługującego usługę Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b34103e521def678acce17e3292e04fca95b5e6e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327982"
---
# <a name="use-azure-storage-explorer-to-manage-access-in-azure-data-lake-storage-gen2"></a>Używanie Eksplorator usługi Azure Storage do zarządzania dostępem w programie Azure Data Lake Storage Gen2

Pliki przechowywane w usłudze Azure Data Lake Storage Gen2 obsługują uprawnienia szczegółowe i zarządzanie listą kontroli dostępu (ACL). Uprawnienia szczegółowe w połączeniu z zarządzaniem listą ACL umożliwiają zarządzanie dostępem do danych na bardzo szczegółowym poziomie.

Ten artykuł zawiera informacje dotyczące wykonywania następujących czynności za pomocą Eksploratora usługi Azure Storage:

> [!div class="checklist"]
> * Ustawianie uprawnień na poziomie pliku
> * Ustawianie uprawnień na poziomie katalogu
> * Dodawanie użytkowników lub grup do listy kontroli dostępu

## <a name="prerequisites"></a>Wymagania wstępne

Aby jak najlepiej przedstawić ten proces, wymagane jest ukończenie [przewodnika Szybki start dotyczącego Eksploratora usługi Azure Storage](data-lake-storage-Explorer.md). Pozwala to zagwarantować, że konto magazynu będzie w najbardziej odpowiednim stanie (utworzonym kontenerze i przekazanym do niego danymi).

## <a name="managing-access"></a>Zarządzanie dostępem

Możesz ustawić uprawnienia w katalogu głównym kontenera. Aby to zrobić, należy zalogować się do Eksplorator usługi Azure Storage przy użyciu indywidualnego konta z uprawnieniami do tego celu (w przeciwieństwie do parametrów połączenia). Kliknij prawym przyciskiem myszy kontener i wybierz pozycję **Zarządzaj uprawnieniami**, a następnie okno dialogowe **Zarządzanie uprawnieniami** .

![Eksplorator usługi Microsoft Azure Storage — zarządzanie dostępem do katalogów](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

Okno dialogowe **Zarządzanie uprawnieniami** służy do zarządzania uprawnieniami właściciela i grupy właściciela. Umożliwia również dodawanie do listy kontroli dostępu nowych użytkowników i grup, dla których można następnie zarządzać uprawnieniami.

Aby dodać do listy kontroli dostępu nowego użytkownika lub nową grupę, wybierz pole **Dodaj użytkownika lub grupę**.

Wprowadź odpowiedni wpis usługi Azure Active Directory (AAD), który chcesz dodać do listy, a następnie wybierz pozycję **Dodaj**.

Użytkownika lub grupę będzie teraz widać w polu **Użytkownicy i grupy:** , co umożliwi rozpoczęcie zarządzania ich uprawnieniami.

> [!NOTE]
> Najlepsze rozwiązanie i zalecenie jest takie, aby utworzyć grupę zabezpieczeń w usłudze AAD i obsługiwać uprawnienia dla grupy, a nie dla poszczególnych użytkowników. Szczegółowe informacje na temat tego zalecenia oraz inne najlepsze rozwiązania można znaleźć w [najlepszych rozwiązaniach dotyczących usługi Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Istnieją dwie kategorie uprawnień, które można przypisać: listy ACL dostępu i domyślne listy ACL.

* **Dostęp**: listy ACL dostępu kontrolują dostęp do obiektu. Pliki i katalogi mają osobne listy ACL dostępu.

* **Domyślnie**: szablon list ACL skojarzonych z katalogiem, który określa listy ACL dostępu dla wszelkich elementów podrzędnych, które są tworzone w tym katalogu. Pliki nie mają domyślnych list ACL.

W ramach obu tych kategorii istnieją trzy uprawnienia, które można następnie przypisać do plików lub katalogów: **Odczyt**, **zapis**i **wykonywanie**.

>[!NOTE]
> Dokonanie wyboru w tym miejscu nie spowoduje ustawienie uprawnień dla wszystkich elementów znajdujących się obecnie w katalogu. Jeśli dany plik już istnieje, należy przejść do poszczególnych elementów i ustawić uprawnienia ręcznie.

Można zarządzać uprawnieniami dla poszczególnych katalogów, a także dla pojedynczych plików, co umożliwia szczegółową kontrolę dostępu. Proces zarządzania uprawnieniami dla plików i katalogów jest taki sam, jak opisano powyżej. Kliknij prawym przyciskiem myszy plik lub katalog, dla którego chcesz zarządzać uprawnieniami, i wykonaj ten sam proces.

## <a name="next-steps"></a>Następne kroki

W tym artykule z instrukcjami przedstawiono sposób ustawiania uprawnień dla plików i katalogów przy użyciu **Eksploratora usługi Azure Storage**. Aby dowiedzieć się więcej o listach ACL, w tym o domyślnych listach ACL, listach ACL dostępu i odpowiadających im uprawnieniach, przejdź do naszego koncepcyjnego artykułu na ten temat.

> [!div class="nextstepaction"]
> [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
