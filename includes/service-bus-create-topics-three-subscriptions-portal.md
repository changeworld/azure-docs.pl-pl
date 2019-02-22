---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ace42278269ff6af31902dbecead81329815af12
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588674"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Tworzenie tematu przy użyciu witryny Azure Portal
1. Na **usługi Service Bus Namespace** wybierz opcję **tematy** w menu po lewej stronie.
2. Wybierz **+ temat** na pasku narzędzi. 
4. Wprowadź **nazwa** tematu. Pozostaw inne opcje z wartościami domyślnymi.
5. Wybierz pozycję **Utwórz**.

    ![Tworzenie tematu](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Tworzenie subskrypcji tematu
1. Wybierz **tematu** utworzoną w poprzedniej sekcji. 
    
    ![Wybierz temat](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. Na **tematu usługi Service Bus** wybierz opcję **subskrypcje** z menu po lewej stronie, a następnie wybierz **+ subskrypcja** na pasku narzędzi. 
    
    ![Dodawanie przycisku subskrypcji](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Na **Utwórz subskrypcję** wpisz **S1** dla **nazwa** dla subskrypcji, a następnie wybierz **Utwórz**. 

    ![Utwórz stronę subskrypcji](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Powtórz poprzedni krok dwa razy, aby tworzyć subskrypcje o nazwie **S2** i **S3**.