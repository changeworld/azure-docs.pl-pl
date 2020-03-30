---
title: Generowanie mapy cieplnej wilgotności gleby
description: W tym artykule opisano sposób generowania mapy ciepła wilgotności gleby w usłudze Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a2115e9c1601c86cce8857c10baf12b91cc2b997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482571"
---
# <a name="generate-soil-moisture-heatmap"></a>Generowanie mapy cieplnej wilgotności gleby

Wilgotność gleby jest wodą, która jest utrzymywana w przestrzeniach między cząstkami gleby.Mapa ciepła wilgotności gleby pomaga zrozumieć dane dotyczące wilgotności na dowolnej głębokości i w wysokiej rozdzielczości w gospodarstwach. Aby uzyskać dokładną i użyteczną mapę cieplną wilgotności gleby, wymagane jest jednolite wdrożenie czujników tego samego dostawcy. Różni dostawcy będą mieli różnice w sposobie pomiaru wilgotności gleby wraz z różnicami w kalibracji. Mapa cieplna jest generowana dla określonej głębokości przy użyciu czujników wdrożonych na tej głębokości.

W tym artykule opisano proces generowania mapy ciepła wilgotności gleby dla farmy przy użyciu akceleratora Azure FarmBeats. W tym artykule dowiesz się, jak:

- [Tworzenie farm](#create-a-farm)
- [Przypisywanie czujników do gospodarstw rolnych](#get-soil-moisture-sensor-data-from-partner)
- [Generowanie mapy cieplnej wilgotności gleby](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Uwaga:  

- Subskrypcja platformy Azure.
- Uruchomione wystąpienie usługi Azure FarmBeats.
- Do gospodarstwa dostępne są co najmniej trzy czujniki wilgotności gleby.

## <a name="create-a-farm"></a>Tworzenie farmy

Gospodarstwo jest obszarem geograficznym zainteresowania, dla którego chcesz utworzyć mapę cieplną wilgotności gleby. Farm można utworzyć za pomocą [interfejsu API Farms](https://aka.ms/FarmBeatsDatahubSwagger) lub interfejsu farm [akceleratora](manage-farms-in-azure-farmbeats.md#create-farms)

## <a name="deploy-sensors"></a>Wdrażanie czujników

W gospodarstwie należy fizycznie wdrożyć czujniki wilgotności gleby. Możesz zakupić czujniki wilgotności gleby od dowolnego z naszych zatwierdzonych partnerów - [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) i [Teralytic.](https://teralytic.com/) Należy koordynować z dostawcą czujników, aby wykonać konfigurację fizyczną w farmie.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>Uzyskaj dane z czujnika wilgotności gleby od partnera

Gdy czujniki rozpoczynają przesyłanie strumieniowe, dane do pulpitu nawigacyjnego danych partnera umożliwiają dostęp do danych w usłudze Azure FarmBeats. Można to zrobić za w aplikacji partnerskiej.

Na przykład jeśli zakupiono czujniki Davis, zalogujesz się do konta łącza pogodowego i podasz wymagane poświadczenia, aby włączyć przesyłanie strumieniowe danych do usługi Azure FarmBeats. Aby uzyskać wymagane poświadczenia, postępuj zgodnie z instrukcjami z [pobierz dane z czujnika](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners).

Po wprowadzeniu poświadczeń i wybierz przycisk **Prześlij** w aplikacji partnera, można mieć dane przepływające do usługi Azure FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Przypisz czujniki wilgotności gleby do gospodarstwa

Po połączeniu konta czujników z platformą Azure FarmBeats należy przypisać czujniki wilgotności gleby do interesującej farmy.

1.  Na stronie głównej wybierz pozycję **Farmy** z menu, zostanie wyświetlona strona listy **Farmy.**
2.  Wybierz **myfarm** > **dodaj urządzenia**.
3.  Zostanie wyświetlone okno **Dodaj urządzenia.** Wybierz dowolne urządzenie, które jest połączone z czujnikami wilgotności gleby w gospodarstwie.

    ![Projekt Farm Beats](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Wybierz **pozycję Dodaj urządzenia**.     

## <a name="generate-soil-moisture-heatmap"></a>Generowanie mapy cieplnej wilgotności gleby

Ten krok polega na utworzeniu zadania lub długotrwałej operacji, która wygeneruje mapę ciepła wilgotności gleby dla twojej farmy.

1.  Na stronie głównej przejdź do **farmy** z lewego menu nawigacji, aby wyświetlić stronę farm.
2.  Wybierz **MyFarm**.
3.  Na stronie **Szczegóły farmy** wybierz pozycję **Generuj dokładność mapy**.
4.  Z menu rozwijanego wybierz opcję **Wilgotność gleby**.
5.  W oknie **Wilgotność gleby** wybierz pozycję **W tym tygodniu**.
6.  W tyczce Wybierz **czujnik** **wilgotności gleby** wprowadź miarę, której chcesz użyć na mapie.
    Aby znaleźć czujnik, w **sensorze**wybierz dowolny czujnik wilgotności gleby. W **polu Właściwości czujnika**należy użyć wartości **Nazwa miary.**

    ![Projekt Farm Beats](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Wybierz **pozycję Generuj mapy**.
    Zostanie wyświetlony komunikat potwierdzający ze szczegółami zadania. Aby uzyskać więcej informacji, zobacz Stan zadania w zadaniach.

    >[!NOTE]
    > Zadanie trwa około trzech do czterech godzin.

### <a name="download-the-soil-moisture-heatmap"></a>Pobierz mapę ciepła wilgotności gleby

Wykonaj następujące czynności:

1. Na stronie **Zadania** sprawdź **stan zadania** dla zadania utworzonego w ostatniej procedurze.
2. Gdy stan zadania zostanie wyświetlony **w programie "Powodze powodem",** wybierz w menu **pozycję Mapy.**
3. Wyszukaj mapę według dnia, w którym została utworzona w formacie <>> gleby moisture_MyFarm_YYYY-MM-DD.
4. Wybierz mapę w kolumnie **Nazwa,** zostanie wyświetlone okno podręczne z podglądem wybranej mapy.
5. Wybierz przycisk **Download** (Pobierz). Mapa zostanie pobrana i zapisana w lokalnym folderze komputera.

    ![Projekt Farm Beats](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy pomyślnie wygenerowano mapę cieplną wilgotności gleby, dowiedz się, jak [wygenerować położenie czujnika](generate-maps-in-azure-farmbeats.md#sensor-placement-map) i [pochłonąć historyczne dane telemetryczne.](ingest-historical-telemetry-data-in-azure-farmbeats.md) 
