---
title: Przewodnik Szybki start platformy Azure — tworzenie klastra usługi Batch AI — Portal | Microsoft Docs
description: Szybki start — tworzenie klastra usługi Batch AI do szkolenia modeli uczenia maszynowego i sztucznej inteligencji — Azure Portal
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 78c743448a7f7439875d3598d6ba5d4eb6dc12fc
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408939"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-portal"></a>Szybki start: tworzenie klastra do zadań szkoleniowych usługi Batch AI przy użyciu witryny Azure Portal

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

W tym przewodniku Szybki start przedstawiono, jak użyć witryny Azure Portal do utworzenia klastra usługi Batch AI, który można wykorzystać do szkolenia modeli sztucznej inteligencji i uczenia maszynowego. Batch AI to zarządzana usługa, która umożliwia naukowcom zajmującym się danymi oraz badaczom sztucznej inteligencji szkolenie modeli sztucznej inteligencji i uczenia maszynowego ze skalowaniem w klastrach z maszynami wirtualnymi platformy Azure.

Klaster ma początkowo jeden węzeł procesora GPU i dołączony serwer plików. Po ukończeniu tego przewodnika Szybki Start powstanie klaster, który będzie można skalować w górę oraz wykorzystać do szkolenia modeli uczenia głębokiego. Prześlij zadania szkoleniowe do klastra przy użyciu usługi Batch AI, narzędzi [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) lub rozszerzenia [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai).

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Do wykonania kroków tego przewodnika Szybki start konieczne jest posiadanie pary kluczy SSH. Jeśli masz już parę kluczy SSH, możesz pominąć ten krok.

Aby utworzyć parę kluczy SSH, uruchom następujące polecenie z powłoki Bash i postępuj zgodnie z wyświetlanymi instrukcjami. Możesz na przykład użyć usługi [Azure Cloud Shell](../cloud-shell/overview.md) lub funkcji [Windows Substem for Linux](/windows/wsl/install-win10) w systemie Windows. Dane wyjściowe polecenia zawierają nazwę pliku klucza publicznego. Skopiuj zawartość pliku klucza publicznego (`cat ~/.ssh/id_rsa.pub`) do schowka lub innej lokalizacji, do której będziesz mieć dostęp w późniejszym kroku.

```bash
ssh-keygen -t rsa -b 2048
```

Bardziej szczegółowe informacje na temat tworzenia pary kluczy SSH znajdują się w artykule [Tworzenie i używanie pary kluczy publiczny-prywatny SSH dla maszyn wirtualnych systemu Linux na platformie Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-batch-ai-workspace"></a>Tworzenie obszaru roboczego usługi Batch AI

Rozpocznij od utworzenia obszaru roboczego usługi Batch AI w celu zorganizowania zasobów usługi Batch AI. Obszar roboczy może zawierać jeden klaster lub większą ich liczbę, a także inne zasoby usługi Batch AI.

1. Wybierz pozycję **Wszystkie usługi** i filtruj, aby znaleźć usługę **Batch AI**.

2. Wybierz pozycję **Dodaj obszar roboczy**.

3. Wprowadź wartości w polach **Nazwa obszaru roboczego** i **Grupa zasobów**. Jeśli chcesz, wybierz różne opcje z list rozwijanych **Subskrypcja** i **Lokalizacja** obszaru roboczego. Wybierz przycisk **Utwórz obszar roboczy**.

  ![Tworzenie obszaru roboczego usługi Batch AI](./media/quickstart-create-cluster-portal/create-workspace.png)

Kiedy pojawi się komunikat **Wdrażanie zakończyło się pomyślnie**, przejdź do utworzonego zasobu i wybierz obszar roboczy.

## <a name="create-a-file-server"></a>Tworzenie serwera plików

Serwer plików usługi Batch AI to jednowęzłowy system plików NFS, który może być automatycznie instalowany w węzłach klastra. Jest jednym z wielu sposobów aprowizacji magazynu dla danych wejściowych i wyjściowych z zadań szkoleniowych.

1. W obszarze roboczym wybierz pozycję **Serwer plików** > **Add Batch AI file server** (Dodaj serwer plików usługi Batch AI).

2. Wprowadź wartości w polach **Nazwa serwera plików** i **Rozmiar maszyny wirtualnej**. W tym przewodniku Szybki start zalecany jest rozmiar maszyny wirtualnej *Standardowa D1_v2* na potrzeby serwera plików. Wybierz inny rozmiar, jeśli chcesz przechowywać większe ilości danych wejściowych lub wyjściowych na potrzeby zadań szkoleniowych.

3. Wprowadź nazwę w polu **Nazwa użytkownika administratora** i skopiuj zawartość pliku klucza publicznego SSH do pola **Klucz SSH**. Zaakceptuj ustawienia domyślne dla pozostałych wartości i wybierz przycisk **Utwórz serwer plików**.

  ![Tworzenie serwera plików usługi Batch AI](./media/quickstart-create-cluster-portal/create-file-server.png)

Wdrożenie serwera plików zajmie kilka minut.

Po utworzeniu serwera, kliknij pozycję **Właściwości** i zwróć uwagę na **Ustawienia instalacji**. Możesz połączyć się protokołem SSH z publicznym adresem IP serwera w celu przekazywania i pobierania danych szkoleniowych i plików wyjściowych we wskazanym katalogu (*/data*).

![Właściwości serwera plików](./media/quickstart-create-cluster-portal/file-server-properties.png)

## <a name="create-a-cluster"></a>Tworzenie klastra

Poniższe kroki umożliwiają utworzenie klastra z jednym węzłem procesora GPU. W węźle klastra działa domyślny obraz serwera Ubuntu przeznaczony do hostowania aplikacji opartych na kontenerach, którego można użyć dla większości obciążeń szkoleniowych. Węzeł klastra instaluje serwer plików w punkcie instalacji. 

1. W obszarze roboczym usługi Batch AI wybierz pozycję **Klaster** > **Add Batch AI cluster** (Dodaj klaster usługi Batch AI).

2. Wprowadź wartość w polu **Nazwa klastra** i określ następujące ustawienia. Sugerowany rozmiar maszyny wirtualnej ma jeden procesor GPU NVIDIA Tesla K80.
  
   |Ustawienie  |Wartość  |
   |---------|---------|
   |**Rozmiar maszyny wirtualnej**     |Standardowa NC6|
   |**Docelowa liczba węzłów**     |1|

3. Wprowadź nazwę w polu **Nazwa użytkownika administratora** i skopiuj zawartość pliku klucza publicznego SSH do pola **Klucz SSH**. Zaakceptuj ustawienia domyślne dla pozostałych wartości na tej stronie, a następnie wybierz przycisk **Dalej: instalacja węzła**.

   ![Wprowadzanie podstawowych informacji dotyczących klastra](./media/quickstart-create-cluster-portal/create-cluster.png)

4. W obszarze **Woluminy instalacji** wybierz pozycję **Odwołania do serwera plików** > **Dodaj**. Wybierz utworzony wcześniej serwer plików. W polu **Ścieżka względna instalacji** wprowadź ścieżkę do instalacji serwera plików w każdym węźle klastra. Wybierz przycisk **Zapisz i kontynuuj**.

   ![Dodawanie odwołań do serwera plików](./media/quickstart-create-cluster-portal/file-server-reference.png)

Zapisz konfigurację węzła, a następnie wybierz pozycję **Utwórz klaster**.

Przydzielenie węzła zajmie usłudze Batch AI kilka minut. W tym czasie **Stan alokacji** klastra będzie miał wartość **Zmiana rozmiaru**. Po kilku minutach stan puli zmieni się na **Stały**, a węzły zostaną uruchomione.

![Uruchamianie klastra](./media/quickstart-create-cluster-portal/cluster-resizing.png)

Wybierz nazwę klastra, aby sprawdzić stan węzła. Gdy węzeł ma stan **Bezczynny**, jest gotowy do uruchamiania zadań szkoleniowych.

### <a name="list-cluster-nodes"></a>Wyświetlenie listy węzłów klastra

Jeśli potrzebujesz nawiązać połączenia z węzłami klastra (w tym przypadku jednym węzłem), aby zainstalować aplikacje lub przeprowadzić konserwację, uzyskaj informacje o połączeniu w portalu. Po utworzeniu klastra kliknij pozycję **Węzły** i zwróć uwagę na ustawienia w sekcji **Połączenie** SSH (adres IP i numer portu).

![Węzły klastra](./media/quickstart-create-cluster-portal/cluster-nodes.png)

Użyj tych informacji, aby nawiązać połączenie SSH z węzłem. Na przykład podstaw poprawny adres IP i numer portu swojego węzła w następującym poleceniu:

```bash
ssh myusername@137.135.82.15 -p 50000
``` 

### <a name="resize-the-cluster"></a>Zmiana rozmiaru klastra

Gdy chcesz użyć klastra do szkolenia modelu, możesz potrzebować więcej zasobów obliczeniowych. Na przykład, aby zwiększyć rozmiar do 2 węzłów na potrzeby zadania rozproszonego szkolenia, wybierz pozycję **Skalowanie** i zmień wartość **Docelowa liczba węzłów** na 2. Zapisz konfigurację.

![Skalowanie klastra](./media/quickstart-create-cluster-portal/scale-cluster.png)

Zmiana rozmiaru klastra zajmie kilka minut.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz kontynuować naukę z samouczkami i przykładami usługi Batch AI, skorzystaj z obszaru roboczego usługi Batch AI, serwera plików i klastra, które zostały utworzone w tym przewodniku Szybki start.

Opłaty za używanie klastra usługi Batch AI i serwera plików są naliczane podczas działania bazowych maszyn wirtualnych, nawet wtedy, gdy nie zostały zaplanowane żadne zadania. Jeśli chcesz zachować konfigurację klastra, gdy nie ma zadań do uruchomienia, zmień rozmiar klastra na 0 węzłów. Później, aby uruchomić zadania, zmień go na 1 lub więcej węzłów. 

Kiedy nie będzie już potrzebny, usuń obszar roboczy usługi Batch AI zawierający klaster i serwer plików. Aby to zrobić, wybierz obszar roboczy usługi Batch AI, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono tworzenie klastra usługi Batch AI i dołączonego serwera plików przy użyciu witryny Azure Portal. Aby uzyskać informacje na temat używania klastra usługi Batch AI do szkolenia modelu, przejdź do przewodnika Szybki start dotyczącego szkolenia modelu uczenia głębokiego.

> [!div class="nextstepaction"]
> [Szkolenie modelu uczenia głębokiego](./quickstart-tensorflow-training-cli.md)
