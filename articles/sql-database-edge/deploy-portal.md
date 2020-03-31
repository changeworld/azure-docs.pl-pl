---
title: Wdrażanie usługi SQL Database Edge Preview przy użyciu witryny Azure Portal | Dokumenty firmy Microsoft
description: Dowiedz się, jak wdrożyć usługę Azure SQL Database Edge przy użyciu portalu Azure
keywords: wdrażanie krawędzi bazy danych SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da922de38d820864b3f83de80fe64eb3ac792e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246725"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Wdrażanie usługi Azure SQL Database Edge Preview

Usługa Azure SQL Database Edge Preview to aparat relacyjnej bazy danych zoptymalizowany pod kątem wdrożeń usługi IoT i usługi Azure IoT Edge. Zapewnia możliwości tworzenia warstwy przechowywania i przetwarzania danych o wysokiej wydajności dla aplikacji i rozwiązań IoT. Ten przewodnik Szybki start pokazuje, jak rozpocząć tworzenie modułu usługi Azure SQL Database Edge za pośrednictwem usługi Azure IoT Edge przy użyciu witryny Azure portal.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Zaloguj się do [Portalu Azure](https://portal.azure.com/).
* Prześlij żądanie [w tym miejscu,](https://azure.microsoft.com/services/sql-database-edge/#contact)aby włączyć subskrypcję do wdrożenia usługi SQL Database Edge.
* Utwórz [centrum Usługi Azure IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* Zarejestruj [urządzenie usługi IoT Edge w witrynie Azure portal](../iot-edge/how-to-register-device-portal.md).
* Przygotuj urządzenie usługi IoT Edge do [wdrożenia modułu usługi IoT Edge z witryny Azure portal](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Aby wdrożyć maszynę wirtualną systemu Azure z systemem Linux jako urządzenie usługi IoT Edge, zobacz ten [przewodnik szybki start.](../iot-edge/quickstart-linux.md)

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>Wdrażanie modułu bazy danych SQL z witryny Azure Marketplace

Azure Marketplace to rynek aplikacji i usług online, w którym można przeglądać szeroką gamę aplikacji i rozwiązań dla przedsiębiorstw, które są certyfikowane i zoptymalizowane do pracy na platformie Azure, w tym [moduły Usługi IoT Edge.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) Usługi Azure SQL Database Edge można wdrożyć na urządzeniu brzegowym za pośrednictwem portalu marketplace.

1. Znajdź moduł usługi Azure SQL Database Edge w portalu Azure Marketplace.<br><br>

   ![Krawędź bazy danych SQL w miejscu: MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Wybierz plan oprogramowania, który najlepiej odpowiada Twoim wymaganiom, i kliknij przycisk **Utwórz**. <br><br>

   ![Wybierz właściwy plan oprogramowania](media/deploy-portal/pick-correct-plan.png)

3. Na stronie Urządzenia docelowe dla modułu krawędzi IoT określ następujące szczegóły, a następnie kliknij przycisk **Utwórz**

   |**Pole**  |**Opis**  |
   |---------|---------|
   |Subskrypcja  |  Subskrypcja platformy Azure, w ramach której utworzono Centrum IoT |
   |Usługa IoT Hub   |  Nazwa centrum IoT Hub, w którym jest zarejestrowane urządzenie Usługi IoT Edge, a następnie wybierz opcję "Wdrażanie na urządzeniu"|
   |Nazwa urządzenia IoT Edge  |  Nazwa urządzenia usługi IoT Edge, na którym zostanie wdrożona usługa SQL Database Edge |

4. Na stronie **Set Modules** przejdź do sekcji dotyczącej modułów wdrażania i kliknij pozycję **Konfiguruj** względem modułu SQL Database Edge. 

5. W okienku **Moduły niestandardowe usługi IoT Edge** określ żądane wartości zmiennych środowiskowych i/lub dostosuj opcje tworzenia i żądane właściwości modułu. Pełną listę obsługiwanych zmiennych środowiskowych można znaleźć w programie [SQL Server Container Environment Variables](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**Parametr**  |**Opis**|
   |---------|---------|
   | Nazwa | Nazwa modułu. |
   |SA_PASSWORD  | Określ silne hasło dla konta administratora usługi SQL Database Edge. |
   |MSSQL_LCID   | Ustawia identyfikator języka używany dla programu SQL Server. Na przykład 1036 jest francuski. |
   |MSSQL_COLLATION | Ustawia domyślne sortowanie dla programu SQL Server. To ustawienie zastępuje domyślne mapowanie identyfikatora języka (LCID) do sortowania. |

   > [!NOTE]
   > Nie należy zmieniać ani aktualizować **identyfikatora URI obrazu** ani ustawień **ACCEPT_EULA** modułu.

6. W okienku **Moduły niestandardowe usługi IoT Edge** zaktualizuj kontener utwórz opcje żądanej wartości **portu hosta**. Jeśli musisz wdrożyć więcej niż jeden moduł usługi SQL DB Edge, należy zaktualizować opcję instalacji, aby utworzyć nową parę docelową & źródłowej dla woluminu trwałego. Aby uzyskać więcej informacji na temat instalacji i woluminu, zobacz [Używanie woluminów](https://docs.docker.com/storage/volumes/) w dokumentacji platformy docker. 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. W okienku **Moduły niestandardowe usługi IoT Edge** zaktualizuj *żądane właściwości modułu Set,* aby uwzględnić lokalizację pakietu SQL i informacje o zadaniu analizy strumienia. Te dwa pola są opcjonalne i powinny być używane, jeśli chcesz wdrożyć moduł usługi SQL Database Edge z bazą danych i zadaniem przesyłania strumieniowego.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. W okienku **Moduły niestandardowe usługi IoT Edge** ustaw *zasadę ponownego uruchamiania* na zawsze i *żądany stan.*
9. W okienku **Moduły niestandardowe usługi IoT Edge** kliknij przycisk **Zapisz**.
10. Na stronie **Ustaw moduły** kliknij przycisk **Dalej**.
11. Na stronie **Określanie trasy (opcjonalnie)** w module **Set Modules** określ trasy dla modułu do modułu lub modułu do komunikacji Usługi IoT Edge Hub, zobacz [Wdrażanie modułów i ustanawianie tras w umywalek IoT.](../iot-edge/module-composition.md)
12. Kliknij przycisk **alej**.
13. Kliknij **przycisk Prześlij**.

W tym przewodniku Szybki start wdrożono moduł krawędzi bazy danych SQL na urządzeniu usługi IoT Edge.

## <a name="next-steps"></a>Następne kroki

- [Uczenie maszynowe i sztuczna inteligencja z ONNX w bazie danych SQL Edge](onnx-overview.md).
- Tworzenie kompleksowego rozwiązania IoT za pomocą usługi SQL Database Edge przy użyciu usługi IoT Edge.
