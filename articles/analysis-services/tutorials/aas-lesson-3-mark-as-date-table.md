---
title: 'Samouczek Azure Analysis Services: lekcja 3 — oznaczanie jako tabeli dat | Microsoft Docs'
description: Opisuje sposób oznaczania jako tabeli dat w projekcie samouczka usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9cbbf8c5ea05915293c785028bdd0a47ba081036
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426026"
---
# <a name="mark-as-date-table"></a>Oznacz jako tabelę dat

W lekcji 2 dotyczącej pobierania danych została zaimportowana tabela wymiarów o nazwie DimDate. W modelu tabela ta nosi nazwę DimDate, ale jest również nazywana *tabelą dat*, ponieważ zawiera dane dotyczące dat i godzin.  
  
Zawsze gdy używasz funkcji analizy czasowej języka DAX (np. podczas opisanego poniżej tworzenia miar), należy określić właściwości, które obejmują *tabelę dat* i unikatowy identyfikator *kolumny dat* w tej tabeli.
  
W tej lekcji oznaczysz tabelę DimDate jako *tabelę dat* i kolumnę Date Column (w tabeli dat) jako *kolumnę dat* (unikatowy identyfikator).  

Zanim oznaczysz tabelę dat i kolumnę dat, warto uporządkować nieco model, aby ułatwić jego zrozumienie. Zwróć uwagę na kolumnę **FullDateAlternateKey** w tabeli DimDate. Zawiera ona jeden wiersz dla każdego dnia w każdym roku kalendarzowym ujętym w tabeli. Ta kolumna jest bardzo często używana w formułach miar i w raportach. Jednak „FullDateAlternateKey” nie jest najlepszym identyfikatorem dla tej kolumny. Możesz zmienić jej nazwę na **Date** (Data), aby móc ją łatwiej zidentyfikować i uprościć sobie dołączanie tej kolumny do formuł. O ile to możliwe, zaleca się zmianę nazw obiektów takich jak tabele i kolumny, aby ułatwić sobie ich identyfikację w programie SSDT i klienckich aplikacjach raportowania (np. Power BI i Excel). 
  
Szacowany czas trwania lekcji: **3 minuty**  
  
## <a name="prerequisites"></a>Wymagania wstępne  
Ten temat stanowi część samouczka modelowania tabelarycznego, który należy wykonać w podanej kolejności. Przed przystąpieniem do wykonywania zadań w tej lekcji należy ukończyć poprzednią lekcję ([Lekcja 2. Pobieranie danych](../tutorials/aas-lesson-2-get-data.md)). 

### <a name="to-rename-the-fulldatealternatekey-column"></a>Aby zmienić nazwę kolumny FullDateAlternateKey

1.  W projektancie modeli kliknij tabelę **DimDate**.

2.  Kliknij dwukrotnie nagłówek kolumny **FullDateAlternateKey**, a następnie zmień jego nazwę na **Date**.

  
### <a name="to-set-mark-as-date-table"></a>Aby oznaczyć jako tabelę dat  
  
1.  Wybierz kolumnę **Date**, a następnie w oknie **Właściwości** w obszarze **Typ danych** upewnij się, że pole **Date** jest zaznaczone.  
  
2.  Kliknij menu **Tabela**, a następnie kliknij przycisk **Date**, po czym kliknij przycisk **Oznacz jako tabelę dat**.  
  
3.  W oknie dialogowym **Oznaczanie jako tabeli dat**, w polu listy **Date** zaznacz kolumnę **Date** jako unikatowy identyfikator kolumny. Zazwyczaj jest ona zaznaczona domyślnie. Kliknij przycisk **OK**. 

    ![aas-lesson3-date-table](../tutorials/media/aas-lesson3-date-table.png)
  

## <a name="whats-next"></a>Co dalej?
[Lekcja 4. Tworzenie relacji](../tutorials/aas-lesson-4-create-relationships.md).
  
