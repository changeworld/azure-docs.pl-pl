---
title: Przewodnik Szybki start platformy Azure — tworzenie obiektu blob w magazynie obiektów przy użyciu witryny Azure Portal | Microsoft Docs
description: W tym przewodniku Szybki start witryna Azure Portal jest używana w ramach magazynu obiektów (blob). Następnie przy użyciu witryny Azure Portal przekażesz obiekt blob do usługi Azure Storage, pobierzesz obiekt blob i wyświetlisz obiekty blob w kontenerze.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: tamram
ms.openlocfilehash: f3d5aaaf57387ae4ccefdfe8ec22435f03b2452f
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757554"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą witryny Azure Portal

Dzięki temu przewodnikowi Szybki start dowiesz się, w jaki sposób za pomocą witryny [Azure portal](https://portal.azure.com/) tworzyć kontener w usłudze Azure Storage oraz przekazywać i pobierać blokowe obiekty blob w ramach tego kontenera.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener w witrynie Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego nowego konta magazynu.
2. W menu po lewej stronie konta magazynu przewiń do sekcji **BLOB Service** , a następnie wybierz pozycję **Containers (kontenery**).
3. Wybierz przycisk **+ Kontener**.
4. Wpisz nazwę nowego kontenera. Nazwa kontenera musi być zapisana małymi literami, zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-). Aby uzyskać dodatkowe informacje o regułach nazewnictwa kontenerów i obiektów blob, zobacz [Nazewnictwo i odwołania do kontenerów, obiektów blob i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).
5. Ustaw poziom dostępu publicznego do kontenera. Domyślny poziom to **Prywatny (bez dostępu anonimowego)** .
6. Wybierz przycisk **OK**, aby utworzyć kontener.

    ![Zrzut ekranu przedstawiający sposób tworzenia kontenera w witrynie Azure Portal](media/storage-quickstart-blobs-portal/create-container.png)

## <a name="upload-a-block-blob"></a>Przekazywanie blokowego obiektu blob

Blokowe obiekty blob składają się z bloków danych złożonych w celu utworzenia obiektu blob. W większości scenariuszy dotyczących użycia usługi Blob Storage używane są blokowe obiekty blob. Blokowe obiekty blob idealnie nadają się do przechowywania tekstu i danych binarnych w chmurze, takich jak pliki, obrazy lub filmy wideo. W tym przewodniku Szybki start przedstawiono sposób pracy z blokowymi obiektami blob. 

Aby przekazać blokowy obiekt blob do nowego kontenera w witrynie Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do kontenera utworzonego w poprzedniej sekcji.
2. Wybierz kontener, aby wyświetlić listę obiektów blob, które zawiera. Ponieważ ten kontener jest nowy, nie będzie jeszcze zawierał żadnych obiektów blob.
3. Wybierz przycisk **Przekaż**, aby przekazać obiekt blob do kontenera.
4. Przejrzyj swój lokalny system plików, aby znaleźć plik do przekazania jako blokowy obiekt blob, a następnie wybierz przycisk **Przekaż**.
     
    ![Zrzut ekranu pokazujący sposób przekazywania obiektu blob z dysku lokalnego](media/storage-quickstart-blobs-portal/upload-blob.png)

5. Wybierz **typ uwierzytelniania**. Wartość domyślna to **SAS**.
6. Przekaż w ten sposób dowolną liczbę obiektów blob. Nowe obiekty blob zostaną wyświetlone w kontenerze.

## <a name="download-a-block-blob"></a>Pobieranie blokowego obiektu blob

Blokowy obiekt blob możesz pobrać, aby wyświetlić go w przeglądarce lub zapisać w lokalnym systemie plików. Aby pobrać blokowy obiekt blob, wykonaj następujące kroki:

1. Przejdź do listy obiektów blob przekazanych w poprzedniej sekcji. 
2. Kliknij prawym przyciskiem myszy obiekt blob, który chcesz pobrać, a następnie wybierz pozycję **Pobierz**. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć zasoby utworzone w tym przewodniku Szybki start, możesz usunąć kontener. Wszystkie obiekty blob w kontenerze również zostaną usunięte.

Aby usunąć kontener:

1. W witrynie Azure Portal przejdź do listy kontenerów w ramach swojego konta magazynu.
2. Wybierz kontener do usunięcia.
3. Wybierz przycisk **Więcej** ( **...** ), a następnie wybierz pozycję **Usuń**.
4. Potwierdź, że chcesz usunąć kontener.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono metodę transferowania plików między dyskiem lokalnym a usługą Azure Blob Storage przy użyciu witryny Azure Portal. Aby dowiedzieć się więcej na temat pracy z usługą Blob Storage, przejdź do instrukcji dotyczących magazynu obiektów blob.

> [!div class="nextstepaction"]
> [Instrukcje: Operacje wykonywane w usłudze Blob Storage](storage-dotnet-how-to-use-blobs.md)

