---
title: Dodawanie magazynu Azure za pomocą usług połączonych w programie Visual Studio | Dokumentacja firmy Microsoft
description: Dodawanie magazynu Azure do aplikacji przy użyciu okna dialogowego programu Visual Studio Dodaj połączone usługi
services: visual-studio-online
author: ghogen
manager: douge
assetId: 521ec044-ad4b-4828-8864-01decde2e758
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/26/2017
ms.author: ghogen
ms.openlocfilehash: 3c5d3dc1d91a6f8bb1816b2985f86ec5c4a12e63
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Dodawanie magazynu platformy Azure przy użyciu programu Visual Studio usługami
Przy użyciu programu Visual Studio możesz można nawiązać żadnego z następujących usługi Azure Storage za pomocą **dodać usług połączonych** okna dialogowego:

- Usługi w chmurze C#
- Usługi mobilnej zaplecza .NET
- Witryny sieci Web ASP.NET lub usługi
- Usługi platformy ASP.NET Core
- Usługa zadań WebJob Azure 

Funkcja podłączonej usługi dodaje wszystkie niezbędne odwołania i kod połączenia do projektu i odpowiednio modyfikuje plików konfiguracji. 

Po zakończeniu **dodać usług połączonych** okno dialogowe automatycznie wyświetla dokumentacji wyszczególnieniem kroków wymaganych do rozpoczęcia pracy z magazynem obiektów blob, kolejek i tabel.

## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Łączenie się z magazynem Azure za pomocą okna dialogowego połączenia usługi
1. Otwórz projekt w programie Visual Studio

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **usług połączonych** węzeł i z menu kontekstowego, a następnie wybierz **dodać podłączonej usługi**.
   
    ![Dodaj Azure połączona usługa](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. W **usług połączonych** wybierz pozycję **magazynu w chmurze z usługą Azure Storage**.
   
    ![Dodawanie magazynu Azure](./media/vs-azure-tools-connected-services-storage/add-azure-storage.png)

1. W **usługi Azure Storage** okno dialogowe, wybierz istniejące konto magazynu i wybierz **Dodaj**.
   
    Jeśli musisz utworzyć konto magazynu, przejdź do następnego kroku. W przeciwnym razie przejdź do kroku 6.
    
    ![Dodaj istniejące konto magazynu do projektu](./media/vs-azure-tools-connected-services-storage/select-azure-storage-account.png)

1. Aby utworzyć konto magazynu: 
   
   1. Wybierz **Utwórz nowe konto magazynu** w dolnej części okna dialogowego.

   1. Wypełnianie **Utwórz konto magazynu** okna dialogowego, a następnie wybierz **Utwórz**.
      
       ![Nowe konto magazynu Azure](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)
      
   1. Gdy **usługi Azure Storage** zostanie wyświetlone okno dialogowe, nowe konto magazynu zostanie wyświetlony na liście. Wybierz nowe konto magazynu na liście, a następnie wybierz **Dodaj**.

1. Magazyn podłączonej usługi pojawia się w obszarze **odwołania do usług** węzła projektu.
   
## <a name="how-your-project-is-modified"></a>Jak zmienić jest projektu
Po zakończeniu okna dialogowego programu Visual Studio dodaje odwołania i modyfikuje niektórych plików konfiguracyjnych. Określone zmiany są zależne od typu projektu: 

- Projekt ASP.NET - [co się stało — projekty programu ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513126)
- Projekt platformy ASP.NET Core - [co się stało — projekty programu ASP.NET 5](http://go.microsoft.com/fwlink/p/?LinkId=513124) 
- Projekt usługi w chmurze (role sieci web i proces roboczy) - [co się stało — projekty usługi w chmurze](http://go.microsoft.com/fwlink/p/?LinkId=516965)
- Projekt zadania WebJob — [co się stało — projekty zadania WebJob](visual-studio/vs-storage-webjobs-what-happened.md)

## <a name="next-steps"></a>Kolejne kroki
- [MSDN Forum: Usługa Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
- [Blog zespołu usługi Magazyn Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- [Dokumentację magazynu platformy Azure](https://docs.microsoft.com/azure/storage/)
