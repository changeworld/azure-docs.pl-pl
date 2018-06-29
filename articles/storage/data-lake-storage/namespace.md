---
title: Namespace hierarchiczna Gen2 wersji zapoznawczej usługi Azure Data Lake Storage
description: Wyjaśnia pojęcie hierarchicznej przestrzeni nazw dla wersji zapoznawczej Gen2 magazynu Azure Data Lake
services: storage
author: jamesbak
manager: twooley
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: f7c3820624a4ef27e2ece4d902f2c033b6a6f48f
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061224"
---
# <a name="azure-data-lake-storage-gen2-preview-hierarchical-namespace"></a>Azure hierarchicznej przestrzeni nazw wersji zapoznawczej usługi Data Lake magazynu Gen2

Mechanizm klucza, który umożliwia podgląd Lake danych Azure Gen2 magazynu zapewnienie wydajności systemu plików na obiekt magazynu skalę i cen, jest dodanie **hierarchicznej przestrzeni nazw**. Umożliwia to zbiór obiektów/plików, w ramach konta, aby organizować w hierarchii katalogów oraz podkatalogów zagnieżdżonych w taki sam sposób, że system plików na tym komputerze jest zorganizowana. W hierarchicznej przestrzeni nazw, włączona Data Lake magazynu Gen2 zapewnia skalowalność i efektywności magazynem obiektów z semantyki systemu plików, które są znane aparaty analizy i platform.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Zalety hierarchicznej przestrzeni nazw

> [!NOTE]
> W publicznej wersji zapoznawczej usługi Azure Data Lake magazynu Gen2 niektóre funkcje wymienione poniżej może się różnić w ich dostępności. Nowe funkcje i regiony zostaną zwolnione w programie wersji zapoznawczej, te informacje będą przekazywane za pośrednictwem naszego dedykowanej grupy Yammer.  

Skojarzone z systemów plików, które implementują hierarchicznej przestrzeni nazw za pośrednictwem danych obiektów blob są następujące korzyści:

- **Niepodzielne manipulowania katalogu:** magazynów obiektu przybliżona hierarchii katalogów przez przyjęcie Konwencji osadzenia ukośnikami (/) w nazwie obiektu do określenia segmentów ścieżki. Gdy tę Konwencję działa prawidłowo organizowania obiektów, Konwencji jest dostępna pomoc nie akcji, takich jak przeniesienie, zmiana nazwy lub usuwanie katalogów. Bez rzeczywistego katalogów aplikacji musi przetworzyć potencjalnie miliony poszczególne obiekty BLOB do osiągnięcia poziomie katalogu zadania. Z kolei hierarchicznej przestrzeni nazw przetwarza te zadania, aktualizując pojedynczy wpis (katalogu nadrzędnego). 

    Optymalizacja znacznej jest szczególnie istotne dla wielu struktur analizy danych big data. Narzędzia, takie jak Hive, Spark, itp. często zapisywania danych wyjściowych do tymczasowej lokalizacji, a następnie zmień lokalizację na zakończenie zadania. Bez hierarchicznej przestrzeni nazw ta zmiana nazwy często może trwać dłużej, niż analytics przetwarzać sam. Mniejsze opóźnienia zadania jest równe niższy całkowity koszt posiadania (TCO) w przypadku obciążeń analizy.

- **Styl interfejsu znanych:** zrozumiałe systemów plików przez deweloperów i użytkowników. Nie istnieje potrzeba aby dowiedzieć się nowego modelu magazynu podczas przenoszenia do chmury, ponieważ interfejs systemu plików udostępnianych przez Data Lake magazynu Gen2 jest tym samym modelu, używany przez komputery, duże i małe.

Jednego z powodów, że obiekt magazynów nie ma w przeszłości obsługiwane hierarchicznej przestrzeni nazw jest, że hierarchicznej przestrzeni nazw ograniczone skali. Jednak hierarchicznej przestrzeni nazw Data Lake magazynu Gen2 zapewnia Skalowanie liniowe i zmniejsza możliwości danych albo wydajności.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Należy włączyć hierarchicznej przestrzeni nazw

Włączanie hierarchicznej przestrzeni nazw jest zalecane dla obciążeń magazynowania, które są przeznaczone dla systemów plików, które manipulowania katalogów. Dotyczy to również wszystkich obciążeń, które są głównie do przetwarzania analytics. Zestawy danych, które wymagają wysokiego stopnia organizacji będą również korzystać przez włączenie hierarchicznej przestrzeni nazw.

Przyczyny włączenie hierarchicznej przestrzeni nazw są określane przez analiza całkowitego kosztu posiadania. Ogólnie rzecz biorąc ulepszenia obciążenia opóźnienia z powodu przyspieszenie magazynu będzie wymagać zasoby obliczeniowe dla mniej czasu. Czas oczekiwania dla wielu zadań mogą być ulepszane z powodu manipulowania atomic katalogu, który został włączony przez hierarchicznej przestrzeni nazw. W wielu zadań zasobów obliczeniowych reprezentuje > 85% całkowitego kosztu i dlatego nawet niewielkie zmniejszenie obciążenia opóźnienia jest równa dużą oszczędność całkowitego kosztu posiadania. Nawet w przypadkach, gdy włączenie hierarchicznej przestrzeni nazw zwiększa kosztów magazynowania całkowitego kosztu posiadania nadal jest obniżona z powodu kosztów zmniejszonej obliczeniowych.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Kiedy należy wyłączyć hierarchicznej przestrzeni nazw

Niektórych obciążeń magazynu obiektu nie może uzyskać korzyści przez włączenie hierarchicznej przestrzeni nazw. Te obciążenia przykłady kopii zapasowych, przechowywania obrazów i inne aplikacje, których organizacja obiektu były przechowywane osobno od same obiekty (*np.*, w oddzielnej bazy danych).

> [!NOTE]
> Przy użyciu wersji zapoznawczej Jeśli włączyć hierarchicznej przestrzeni nazw nie ma żadnych współdziałanie danych lub operacji między obiektów Blob i interfejsów API REST Gen2 Data Lake magazynu. Ta funkcja zostanie dodana w wersji zapoznawczej.

## <a name="next-steps"></a>Kolejne kroki

- [Utwórz konto magazynu](./quickstart-create-account.md)