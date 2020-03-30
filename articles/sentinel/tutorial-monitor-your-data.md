---
title: Wizualizuj dane za pomocą pulpitów nawigacyjnych opartych na skoroszytach usługi Azure Monitor w usłudze Azure Sentinel | Dokumenty firmy Microsoft
description: Użyj tego samouczka, aby dowiedzieć się, jak wizualizować dane przy użyciu pulpitów nawigacyjnych na podstawie skoroszytów w usłudze Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/01/2020
ms.author: yelevin
ms.openlocfilehash: b4461ac43e9356536914b345ef28f5de62fc9f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585224"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Samouczek: Wizualizuj i monitoruj swoje dane



Po [podłączeniu źródeł danych](quickstart-onboard.md) do usługi Azure Sentinel można wizualizować i monitorować dane przy użyciu wdrażania skoroszytów usługi Azure Sentinel usługi Azure Sentinel, co zapewnia wszechstronność w tworzeniu niestandardowych pulpitów nawigacyjnych. Skoroszyty są wyświetlane w inny sposób w usłudze Azure Sentinel, ale może być przydatne, aby zobaczyć, jak [tworzyć interaktywne raporty za pomocą skoroszytów usługi Azure Monitor](../azure-monitor/app/usage-workbooks.md). Usługa Azure Sentinel umożliwia tworzenie niestandardowych skoroszytów w danych, a także jest dostarczany z wbudowanymi szablonami skoroszytu, które umożliwiają szybkie uzyskiwanie szczegółowych informacji na temat danych zaraz po podłączeniu źródła danych.


Ten samouczek ułatwia wizualizację danych w usłudze Azure Sentinel.
> [!div class="checklist"]
> * Używanie wbudowanych skoroszytów
> * Tworzenie nowych skoroszytów

## <a name="prerequisites"></a>Wymagania wstępne

- W grupie zasobów obszaru roboczego usługi Azure Sentinel musi być co najmniej uprawnienia czytnika skoroszytu lub współautora skoroszytu.

> [!NOTE]
> Skoroszyty widoczne w usłudze Azure Sentinel są zapisywane w grupie zasobów obszaru roboczego usługi Azure Sentinel i oznaczane przez obszar roboczy, w którym zostały utworzone.

## <a name="use-built-in-workbooks"></a>Używanie wbudowanych skoroszytów

1. Przejdź do **skoroszytów,** a następnie wybierz **pozycję Szablony,** aby wyświetlić pełną listę wbudowanych skoroszytów usługi Azure Sentinel. Aby zobaczyć, które są istotne dla typów danych, które zostały połączone, wymagane **typy danych** w każdym skoroszycie wyświetli listę typu danych obok zielonego znacznika wyboru, jeśli już przesyłasz strumieniowo odpowiednie dane do usługi Azure Sentinel.
  ![przejdź do skoroszytów](./media/tutorial-monitor-data/access-workbooks.png)
1. Kliknij **pozycję Wyświetl skoroszyt,** aby wyświetlić szablon wypełniony danymi.
  
1. Aby edytować skoroszyt, wybierz pozycję **Zapisz**, a następnie wybierz lokalizację, w której chcesz zapisać plik json dla szablonu. 

   > [!NOTE]
   > Spowoduje to utworzenie zasobu platformy Azure na podstawie odpowiedniego szablonu i zapisuje sam plik Json szablonu, a nie dane.


1. Wybierz pozycję **Wyświetl skoroszyt**. Następnie kliknij przycisk **Edytuj** u góry. Teraz możesz edytować skoroszyt i dostosować go do swoich potrzeb. Aby uzyskać więcej informacji na temat dostosowywania skoroszytu, zobacz jak [tworzyć interaktywne raporty za pomocą skoroszytów monitora Azure](../azure-monitor/app/usage-workbooks.md).
![wyświetlanie skoroszytów](./media/tutorial-monitor-data/workbook-graph.png)
1. Po włożnieniu zmian można zapisać skoroszyt. 

1. Skoroszyt można również sklonować: Wybierz pozycję **Edytuj,** a następnie **zapisz jako**, zapisując go pod inną nazwą w ramach tej samej subskrypcji i grupy zasobów. Te skoroszyty są wyświetlane na karcie **Moje skoroszyty.**


## <a name="create-new-workbook"></a>Tworzenie nowego skoroszytu

1. Przejdź do **skoroszytów,** a następnie wybierz pozycję **Dodaj skoroszyt,** aby utworzyć nowy skoroszyt od podstaw.
  ![przejdź do skoroszytów](./media/tutorial-monitor-data/create-workbook.png)

1. Aby edytować skoroszyt, wybierz pozycję **Edytuj**, a następnie w razie potrzeby dodaj tekst, kwerendy i parametry. Aby uzyskać więcej informacji na temat dostosowywania skoroszytu, zobacz jak [tworzyć interaktywne raporty za pomocą skoroszytów monitora Azure](../azure-monitor/app/usage-workbooks.md). 

1. Podczas tworzenia kwerendy ustaw **źródło danych** na **Dzienniki,** **typ zasobu** jest ustawiony na **Usługa Log Analytics,** a następnie wybierz odpowiednie obszary robocze. 

1. Po utworzeniu skoroszytu zapisz skoroszyt, upewniając się, że zapiszesz go w ramach subskrypcji i grupy zasobów obszaru roboczego usługi Azure Sentinel.

1. Jeśli chcesz, aby inne osoby w organizacji korzystały ze skoroszytu, w obszarze **Zapisz** wybierz pozycję **Udostępnione raporty**. Jeśli chcesz, aby ten skoroszyt był dostępny tylko dla Ciebie, wybierz **pozycję Moje raporty**.

1. Aby przełączać się między skoroszytami w obszarze](./media/tutorial-monitor-data/switch.png)roboczym, możesz wybrać pozycję **Otwórz** ![skoroszyty przełącznika w górnym okienku dowolnego skoroszytu. W oknie otwiera się po prawej stronie przełączaj się między skoroszytami.

   ![Przełączanie skoroszytów](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Jak usunąć skoroszyty

Można usunąć skoroszyty, które zostały utworzone na podstawie szablonu Usługi Azure Sentinel. 

Aby usunąć dostosowany skoroszyt, na stronie Skoroszyty zaznacz zapisany skoroszyt, który chcesz usunąć, i wybierz pozycję **Usuń**. Spowoduje to usunięcie zapisanego skoroszytu.

> [!NOTE]
> Spowoduje to usunięcie zasobu, a także wszelkich zmian wprowadzonych w szablonie. Oryginalny szablon pozostanie dostępny.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak wyświetlić dane w usłudze Azure Sentinel.

Aby dowiedzieć się, jak zautomatyzować reagowanie na zagrożenia, zobacz [Konfigurowanie automatycznych odpowiedzi na zagrożenia w usłudze Azure Sentinel.](tutorial-respond-threats-playbook.md)
