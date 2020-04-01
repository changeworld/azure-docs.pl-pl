---
title: Samouczek — eksportowanie danych i wizualizacja szczegółowych informacji w usłudze Azure IoT Central
description: W tym samouczku dowiesz się, jak eksportować dane z usługi IoT Central i wizualizować szczegółowe informacje na pulpicie nawigacyjnym usługi Power BI.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: 9dcb185ab8375d46c75a12e6adaeeae2358c13ac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77022090"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Samouczek: eksportowanie danych z usługi Azure IoT Central i wizualizacja szczegółowych informacji w usłudze Power BI



W dwóch poprzednich samouczkach utworzono i dostosowano aplikację IoT Central przy użyciu szablonu aplikacji **analizy w sklepie** . W tym samouczku skonfigurujesz aplikację IoT Central do eksportowania danych telemetrycznych zebranych z urządzeń. Następnie można użyć usługi Power BI do utworzenia niestandardowego pulpitu nawigacyjnego dla menedżera sklepu w celu wizualizacji szczegółowych informacji pochodzących z danych telemetrycznych.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj aplikację IoT Central, aby wyeksportować dane telemetryczne do centrum zdarzeń.
> * Aplikacje logiki używają do wysyłania danych z centrum zdarzeń do zestawu danych przesyłania strumieniowego usługi Power BI.
> * Utwórz pulpit nawigacyjny usługi Power BI, aby wizualizować dane w zestawie danych przesyłania strumieniowego.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Aby ukończyć dwa poprzednie samouczki, [utwórz aplikację analityczną w sklepie w usłudze Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) i [dostosuj pulpit nawigacyjny operatora i zarządzaj urządzeniami w usłudze Azure IoT Central](./tutorial-in-store-analytics-customize-dashboard-pnp.md).
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.
* Konto usługi Power BI. Jeśli nie masz konta usługi Power BI, przed rozpoczęciem zarejestruj się w celu uzyskania [bezpłatnej wersji próbnej usługi Power BI Pro.](https://app.powerbi.com/signupredirect?pbi_source=web)

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem centrum zdarzeń i aplikacji logiki, należy utworzyć grupę zasobów, aby nimi zarządzać. Grupa zasobów powinna znajdować się w tej samej lokalizacji co **aplikacja analizy w sklepie — wyewidencjonuj** aplikację IoT Central. Aby utworzyć grupę zasobów:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W lewej nawigacji wybierz pozycję **Grupy zasobów**. Następnie wybierz pozycję **Dodaj**.
1. W przypadku **subskrypcji**wybierz nazwę subskrypcji platformy Azure użytej do utworzenia aplikacji IoT Central.
1. W przypadku **nazwy grupy zasobów** wprowadź analizę sklepu _detalicznego_*.
1. W przypadku **regionu**wybierz ten sam region, który został wybrany dla aplikacji IoT Central.
1. Wybierz **pozycję Recenzja + Utwórz**.
1. Na stronie **Przeglądanie + tworzenie** wybierz pozycję **Utwórz**.

Masz teraz grupę zasobów o nazwie **retail-store-analysis** w ramach subskrypcji.

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Przed skonfigurowaniem aplikacji monitorowania sieci sprzedaży do eksportowania danych telemetrycznych należy utworzyć centrum zdarzeń, aby odbierać eksportowane dane. Poniższe kroki pokazują, jak utworzyć centrum zdarzeń:

1. W witrynie Azure portal wybierz pozycję **Utwórz zasób** w lewym górnym rogu ekranu.
1. W **polu Wyszukaj w marketplace**wprowadź centrum _zdarzeń_, a następnie naciśnij klawisz **Enter**.
1. Na stronie **Centra zdarzeń** wybierz pozycję **Utwórz**.
1. Na stronie **Tworzenie obszaru nazw** należy wykonać następujące czynności:
    * Wprowadź unikatową nazwę obszaru nazw, taką jak _twojanazwa-retail-store-analysis_. System sprawdza, czy ta nazwa jest dostępna.
    * Wybierz **podstawową** warstwę cenową.
    * Wybierz tę samą **subskrypcję,** która została użyta do utworzenia aplikacji IoT Central.
    * Wybierz grupę zasobów **analizy sklepu detalicznego.**
    * Wybierz tę samą lokalizację, która została użyta w aplikacji IoT Central.
    * Wybierz **pozycję Utwórz**. Może być trzeba poczekać kilka minut na system do aprowizowania zasobów.
1. W portalu przejdź do grupy zasobów **analizy magazynu sieci sprzedaży.** Zaczekaj na zakończenie wdrożenia. Może być konieczne **wybranie opcji Odśwież,** aby zaktualizować stan wdrożenia. Stan utworzenia obszaru nazw centrum zdarzeń można również sprawdzić w obszarze **powiadomień**.
1. W grupie zasobów **analizy sklepu detalicznego** wybierz **obszar nazw centrów zdarzeń**. Zostanie wyświetlona strona główna **obszaru nazw centrów zdarzeń** w portalu.

Teraz masz **obszar nazw centrów zdarzeń,** możesz utworzyć **Centrum zdarzeń** do użycia z aplikacją IoT Central:

1. Na stronie głównej **obszaru nazw centrów zdarzeń** w portalu wybierz pozycję **+ Centrum zdarzeń**.
1. Na stronie **Tworzenie Centrum zdarzeń** wprowadź dane _telemetryczne magazynu_ jako nazwę, a następnie wybierz pozycję **Utwórz**.

Teraz masz centrum zdarzeń, którego można używać podczas konfigurowania eksportu danych z aplikacji IoT Central:

![Centrum zdarzeń](./media/tutorial-in-store-analytics-visualize-insights-pnp/event-hub.png)

## <a name="configure-data-export"></a>Konfigurowanie eksportu danych

Teraz masz centrum zdarzeń, możesz skonfigurować aplikację **analityczną w sklepie — wyewidencjonuj,** aby eksportować dane telemetryczne z podłączonych urządzeń. Poniższe kroki pokazują, jak skonfigurować eksport:

1. Zaloguj się do **analizy w sklepie — wyewidencjonuj** aplikację IoT Central.
1. Wybierz **pozycję Eksportowanie danych** w lewym okienku.
1. Wybierz **pozycję Nowy > usługi Azure Event Hubs**.
1. Wprowadź _eksport telemetrii_ jako **nazwę wyświetlaną**.
1. Wybierz **obszar nazw Centrów zdarzeń**.
1. Wybierz centrum zdarzeń **telemetrii sklepu.**
1. Wyłącz **urządzenia** i **szablony urządzeń** w sekcji **Dane, aby wyeksportować.**
1. Wybierz **pozycję Zapisz**.

Eksport danych może potrwać kilka minut, aby rozpocząć wysyłanie danych telemetrycznych do centrum zdarzeń. Stan eksportu można zobaczyć na stronie **Eksportowanie danych:**

![Ciągła konfiguracja eksportu danych](./media/tutorial-in-store-analytics-visualize-insights-pnp/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Tworzenie zestawów danych usługi Power BI

Pulpit nawigacyjny usługi Power BI wyświetli dane z aplikacji do monitorowania sprzedaży detalicznej. W tym rozwiązaniu używasz zestawów danych przesyłania strumieniowego usługi Power BI jako źródła danych dla pulpitu nawigacyjnego usługi Power BI. W tej sekcji można zdefiniować schemat zestawów danych przesyłania strumieniowego, tak aby aplikacja logiki można przesyłać dalej dane z centrum zdarzeń. Poniższe kroki pokazują, jak utworzyć dwa zestawy danych przesyłania strumieniowego dla czujników środowiskowych i jeden zestaw danych przesyłania strumieniowego dla czujnika obłożenia:

1. Zaloguj się do swojego konta usługi **Power BI**.
1. Wybierz pozycję **Obszary robocze**, a następnie wybierz pozycję **Utwórz obszar roboczy**.
1. Na stronie **Tworzenie obszaru roboczego** wprowadź _pozycję Analiza w sklepie — wyewidencjonowanie_ jako **nazwa obszaru roboczego**.
1. Przewiń do dołu strony Witamy na stronie **obszaru roboczego Analizy w sklepie — wyewidencjonuj,** a następnie wybierz pozycję **Pomiń**.
1. Na stronie obszaru roboczego wybierz pozycję **Utwórz > zestaw danych przesyłania strumieniowego**.
1. Na stronie **Nowy zestaw danych przesyłania strumieniowego** wybierz pozycję **API**, a następnie wybierz pozycję **Dalej**.
1. Wprowadź _czujnik strefy 1_ jako **nazwę zestawu danych**.
1. Wprowadź trzy **wartości ze strumienia** w poniższej tabeli:

    | Nazwa wartości  | Typ wartości |
    | ----------- | ---------- |
    | Znacznik czasu   | DateTime   |
    | Wilgotność    | Liczba     |
    | Temperatura | Liczba     |

1. **Włącz analizę danych historycznych.**
1. Wybierz **pozycję Utwórz,** a następnie **gotowe**.
1. Utwórz kolejny zestaw danych przesyłania strumieniowego o nazwie **Czujnik Strefy 2** z tym samym schematem i ustawieniami co zestaw danych strumieniowych **czujnika strefy 1.**

Masz teraz dwa zestawy danych przesyłania strumieniowego. Aplikacja logiki będzie kierować dane telemetryczne z dwóch czujników środowiskowych podłączonych do **analizy w sklepie —** aplikacja do realizacji transakcji do tych dwóch zestawów danych:

![Zestawy danych strefy](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-1.png)

To rozwiązanie używa jednego zestawu danych przesyłania strumieniowego dla każdego czujnika, ponieważ nie jest możliwe zastosowanie filtrów do przesyłania strumieniowego danych w usłudze Power BI.

Potrzebny jest również zestaw danych przesyłania strumieniowego dla danych telemetrycznych obłożenia:

1. Na stronie obszaru roboczego wybierz pozycję **Utwórz > zestaw danych przesyłania strumieniowego**.
1. Na stronie **Nowy zestaw danych przesyłania strumieniowego** wybierz pozycję **API**, a następnie wybierz pozycję **Dalej**.
1. Wprowadź _czujnik obłożenia_ jako **nazwę zestawu danych**.
1. Wprowadź pięć **wartości ze strumienia** w poniższej tabeli:

    | Nazwa wartości     | Typ wartości |
    | -------------- | ---------- |
    | Znacznik czasu      | DateTime   |
    | Długość kolejki 1 | Liczba     |
    | Długość kolejki 2 | Liczba     |
    | Czas przebywania 1   | Liczba     |
    | Czas przebywania 2   | Liczba     |

1. **Włącz analizę danych historycznych.**
1. Wybierz **pozycję Utwórz,** a następnie **gotowe**.

Masz teraz trzeci zestaw danych przesyłania strumieniowego, który przechowuje wartości z symulowanego czujnika obłożenia. Ten czujnik raportuje długość kolejki przy dwóch kasach w sklepie oraz czas oczekiwania klientów w tych kolejkach:

![Zestaw danych o obłożeniem](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-2.png)

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

W tym rozwiązaniu aplikacja logiki odczytuje dane telemetryczne z centrum zdarzeń, analizuje dane, a następnie wysyła je do utworzonych zestawów danych przesyłania strumieniowego usługi Power BI.

Przed utworzeniem aplikacji logiki, należy identyfikatory urządzeń dwóch czujników RuuviTag podłączony do aplikacji IoT Central w [Tworzenie aplikacji analitycznej w sklepie w usłudze Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) samouczka:

1. Zaloguj się do **analizy w sklepie — wyewidencjonuj** aplikację IoT Central.
1. Wybierz **pozycję Urządzenia** w lewym okienku. Następnie wybierz **RuuviTag**.
1. Zanotuj **identyfikatory urządzeń**. Na poniższym zrzucie ekranu identyfikatory to **f5dcf4ac32e8** i **e29ffc8d5326**:

    ![Identyfikatory urządzeń](./media/tutorial-in-store-analytics-visualize-insights-pnp/device-ids.png)

Poniższe kroki pokazują, jak utworzyć aplikację logiki w witrynie Azure portal:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz pozycję **Utwórz zasób** w lewym górnym rogu ekranu.
1. W **polu Wyszukaj w portalu Marketplace**wprowadź aplikację _Logika_, a następnie naciśnij **klawisz Enter**.
1. Na stronie **Aplikacja logiki** wybierz pozycję **Utwórz**.
1. Na stronie tworzenie **aplikacji logiki:**
    * Wprowadź unikatową nazwę aplikacji logiki, taką jak _twojanazwa-retail-store-analysis_.
    * Wybierz tę samą **subskrypcję,** która została użyta do utworzenia aplikacji IoT Central.
    * Wybierz grupę zasobów **analizy sklepu detalicznego.**
    * Wybierz tę samą lokalizację, która została użyta w aplikacji IoT Central.
    * Wybierz **pozycję Utwórz**. Może być trzeba poczekać kilka minut na system do aprowizowania zasobów.
1. W witrynie Azure portal przejdź do nowej aplikacji logiki.
1. Na stronie **Projektant aplikacji logiki** przewiń w dół i wybierz pozycję **Pusta aplikacja logiki**.
1. W **polu Wyszukaj łączniki i wyzwalacze**wprowadź centrum _zdarzeń_.
1. W **obszarze Wyzwalacze**wybierz pozycję **Kiedy zdarzenia są dostępne w Centrum zdarzeń**.
1. Wprowadź _dane telemetryczne Magazynu_ jako nazwę **połączenia**i wybierz obszar **nazw centrów zdarzeń**.
1. Wybierz zasadę **RootManageSharedAccess** i wybierz pozycję **Utwórz**.
1. W akcji **Kiedy zdarzenia są dostępne w Centrum zdarzeń:**
    * W **nazwie Centrum zdarzeń**wybierz opcję przechowywanie danych **telemetrycznych**.
    * W **obszarze Typ zawartości**wybierz **aplikację/json**.
    * Ustaw **interwał** na trzy, a **częstotliwość** na sekundy
1. Wybierz **pozycję Zapisz,** aby zapisać aplikację logiki.

Aby dodać logikę do projektu aplikacji logiki, wybierz **widok Kod:**

1. Wymień `"actions": {},` na następujący JSON. Wymień dwa `[YOUR RUUVITAG DEVICE ID 1]` symbole zastępcze i `[YOUR RUUVITAG DEVICE ID 2]` identyfikatory, które zanotowałeś w dwóch urządzeniach RuuviTag:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. Wybierz **pozycję Zapisz,** a następnie wybierz pozycję **Projektant,** aby wyświetlić wersję wizualną dodanej logiki:

    ![Projektowanie aplikacji logiki](./media/tutorial-in-store-analytics-visualize-insights-pnp/logic-app.png)

1. Wybierz **opcję Przełącz według identyfikatora urządzenia,** aby rozwinąć akcję. Następnie wybierz **opcję Strefa 1 środowiska**i wybierz pozycję Dodaj **akcję**.
1. W **polu Szukaj łączników i akcji**wprowadź program Power **BI**, a następnie naciśnij klawisz **Enter**.
1. Zaznacz akcję **Dodaj wiersze do zestawu danych (podglądu).**
1. Wybierz **pozycję Zaloguj się** i postępuj zgodnie z monitami, aby zalogować się na swoje konto usługi Power BI.
1. Po zakończeniu procesu logowania w akcji **Dodaj wiersze do zestawu danych:**
    * Wybierz **analitykę w sklepie — jako** obszar roboczy.
    * Wybierz **czujnik strefy 1** jako zestaw danych.
    * Wybierz **RealTimeData** jako tabelę.
    * Wybierz **pozycję Dodaj nowy parametr,** a następnie wybierz pola **Sygnatura** **czasowa, Wilgotność**i **Temperatura.**
    * Zaznacz pole **Sygnatura czasowa,** a następnie wybierz **x-opt-enqueuedtime** z listy **Zawartość dynamiczna.**
    * Zaznacz pole **Wilgotność,** a następnie wybierz pozycję **Zobacz więcej** obok **pozycji Analizuj dane telemetryczne**. Następnie wybierz **wilgotność**.
    * Zaznacz pole **Temperatura,** a następnie wybierz pozycję **Zobacz więcej** obok **pozycji Analizuj dane telemetryczne**. Następnie wybierz **opcję Temperatura**.
    * Wybierz przycisk **Zapisz**, aby zapisać zmiany. Akcja **środowiska strefa 1** wygląda następująco: ![Środowisko strefy 1](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-1-action.png)
1. Wybierz akcję **Środowisko strefy 2** i wybierz pozycję **Dodaj akcję**.
1. W **polu Szukaj łączników i akcji**wprowadź program Power **BI**, a następnie naciśnij klawisz **Enter**.
1. Zaznacz akcję **Dodaj wiersze do zestawu danych (podglądu).**
1. W akcji **Dodawanie wierszy do zestawu danych 2:**
    * Wybierz **analitykę w sklepie — jako** obszar roboczy.
    * Wybierz **czujnik strefy 2** jako zestaw danych.
    * Wybierz **RealTimeData** jako tabelę.
    * Wybierz **pozycję Dodaj nowy parametr,** a następnie wybierz pola **Sygnatura** **czasowa, Wilgotność**i **Temperatura.**
    * Zaznacz pole **Sygnatura czasowa,** a następnie wybierz **x-opt-enqueuedtime** z listy **Zawartość dynamiczna.**
    * Zaznacz pole **Wilgotność,** a następnie wybierz pozycję **Zobacz więcej** obok **pozycji Analizuj dane telemetryczne**. Następnie wybierz **wilgotność**.
    * Zaznacz pole **Temperatura,** a następnie wybierz pozycję **Zobacz więcej** obok **pozycji Analizuj dane telemetryczne**. Następnie wybierz **opcję Temperatura**.
    Wybierz przycisk **Zapisz**, aby zapisać zmiany.  Akcja **środowiska strefy 2** wygląda następująco: ![Środowisko strefy 2](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-2-action.png)
1. Wybierz akcję **Obłożenie,** a następnie wybierz akcję **Przełącz według identyfikatora interfejsu.**
1. Wybierz akcję **interfejsu Czas przebywania** i wybierz pozycję **Dodaj akcję**.
1. W **polu Szukaj łączników i akcji**wprowadź program Power **BI**, a następnie naciśnij klawisz **Enter**.
1. Zaznacz akcję **Dodaj wiersze do zestawu danych (podglądu).**
1. W akcji **Dodawanie wierszy do zestawu danych:**
    * Wybierz **analitykę w sklepie — jako** obszar roboczy.
    * Wybierz **opcję Czujnik obłożenia** jako zestaw danych.
    * Wybierz **RealTimeData** jako tabelę.
    * Wybierz **pozycję Dodaj nowy parametr,** a następnie wybierz pola **Sygnatura czasowa**, **Czas przebywania 1**i **Czas przebywania 2.**
    * Zaznacz pole **Sygnatura czasowa,** a następnie wybierz **x-opt-enqueuedtime** z listy **Zawartość dynamiczna.**
    * Wybierz pole **Czas przebywania 1,** a następnie wybierz pozycję **Zobacz więcej** obok **pozycji Analizuj dane telemetryczne**. Następnie wybierz **DwellTime1**.
    * Wybierz pole **Czas przebywania 2,** a następnie wybierz pozycję **Zobacz więcej** obok **pozycji Analizuj dane telemetryczne**. Następnie wybierz **DwellTime2**.
    * Wybierz przycisk **Zapisz**, aby zapisać zmiany. Akcja **interfejsu Czasu przebywania** wygląda następująco: Akcja ![obłożenia](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-1.png)
1. Wybierz akcję **interfejsu Liczba osób** i wybierz pozycję Dodaj **akcję**.
1. W **polu Szukaj łączników i akcji**wprowadź program Power **BI**, a następnie naciśnij klawisz **Enter**.
1. Zaznacz akcję **Dodaj wiersze do zestawu danych (podglądu).**
1. W akcji **Dodawanie wierszy do zestawu danych:**
    * Wybierz **analitykę w sklepie — jako** obszar roboczy.
    * Wybierz **opcję Czujnik obłożenia** jako zestaw danych.
    * Wybierz **RealTimeData** jako tabelę.
    * Wybierz **pozycję Dodaj nowy parametr,** a następnie wybierz pola **Sygnatura czasowa**, **Długość kolejki 1**i **Długość kolejki 2.**
    * Zaznacz pole **Sygnatura czasowa,** a następnie wybierz **x-opt-enqueuedtime** z listy **Zawartość dynamiczna.**
    * Zaznacz pole **Długość kolejki 1,** a następnie wybierz pozycję **Zobacz więcej** obok **pozycji Analizowanie danych telemetrycznych**. Następnie wybierz **count1**.
    * Zaznacz pole **Długość kolejki 2,** a następnie wybierz pozycję **Zobacz więcej** obok **pozycji Analizowanie danych telemetrycznych**. Następnie wybierz **count2**.
    * Wybierz przycisk **Zapisz**, aby zapisać zmiany. Akcja **interfejsu Liczba osób** wygląda następująco na zrzucie ekranu: ![Akcja obłożenia](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-2.png)

Aplikacja logiki działa automatycznie. Aby wyświetlić stan każdego uruchomienia, przejdź do strony **Przegląd** aplikacji logiki w witrynie Azure Portal:

## <a name="create-a-power-bi-dashboard"></a>Tworzenie pulpitu nawigacyjnego usługi Power BI

Teraz masz dane telemetryczne płynące z aplikacji IoT Central za pośrednictwem centrum zdarzeń. Następnie aplikacja logiki analizuje komunikaty centrum zdarzeń i dodaje je do zestawu danych przesyłania strumieniowego usługi Power BI. Teraz można utworzyć pulpit nawigacyjny usługi Power BI do wizualizacji danych telemetrycznych:

1. Zaloguj się do swojego konta usługi **Power BI**.
1. Wybierz **obszary robocze > analityki w sklepie — wyewidencjonowanie**.
1. Wybierz pozycję **Utwórz > pulpit nawigacyjny**.
1. Wprowadź **analizę sklepu** jako nazwę pulpitu nawigacyjnego i wybierz pozycję **Utwórz**.

### <a name="add-line-charts"></a>Dodawanie wykresów liniowych

Dodaj cztery płytki wykresu liniowego, aby pokazać temperaturę i wilgotność z dwóch czujników środowiskowych. Użyj informacji w poniższej tabeli, aby utworzyć kafelki. Aby dodać każdy kafelek, zacznij od wybrania **... (Więcej opcji) > Dodaj kafelek**. Wybierz **pozycję Niestandardowe dane przesyłania strumieniowego**, a następnie wybierz pozycję **Dalej:**

| Ustawienie | Wykres #1 | Wykres #2 | Wykres #3 | #4 wykresu |
| ------- | -------- | -------- | -------- | -------- |
| Dataset | Czujnik strefy 1 | Czujnik strefy 1 | Czujnik strefy 2 | Czujnik strefy 2 |
| Typ wizualizacji | Wykres liniowy | Wykres liniowy | Wykres liniowy | Wykres liniowy |
| Oś | Znacznik czasu | Znacznik czasu | Znacznik czasu | Znacznik czasu |
| Wartości | Temperatura | Wilgotność | Temperatura | Wilgotność |
| Okno czasu | 60 min. | 60 min. | 60 min. | 60 min. |
| Tytuł | Temperatura (1 godzina) | Wilgotność (1 godzina) | Temperatura (1 godzina) | Wilgotność (1 godzina) |
| Podtytuł | Strefa 1 | Strefa 1 | Strefa 2 | Strefa 2 |

Poniższy zrzut ekranu przedstawia ustawienia pierwszego wykresu:

![Ustawienia wykresu liniowego](./media/tutorial-in-store-analytics-visualize-insights-pnp/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Dodawanie kart do pokazania danych środowiskowych

Dodaj cztery płytki karty, aby wyświetlić najnowsze wartości temperatury i wilgotności z dwóch czujników środowiskowych. Użyj informacji w poniższej tabeli, aby utworzyć kafelki. Aby dodać każdy kafelek, zacznij od wybrania **... (Więcej opcji) > Dodaj kafelek**. Wybierz **pozycję Niestandardowe dane przesyłania strumieniowego**, a następnie wybierz pozycję **Dalej:**

| Ustawienie | #1 karty | #2 karty | #3 karty | #4 karty |
| ------- | ------- | ------- | ------- | ------- |
| Dataset | Czujnik strefy 1 | Czujnik strefy 1 | Czujnik strefy 2 | Czujnik strefy 2 |
| Typ wizualizacji | Karta | Karta | Karta | Karta |
| Pola | Temperatura | Wilgotność | Temperatura | Wilgotność |
| Tytuł | Temperatura (F) | Wilgotność (%) | Temperatura (F) | Wilgotność (%) |
| Podtytuł | Strefa 1 | Strefa 1 | Strefa 2 | Strefa 2 |

Poniższy zrzut ekranu przedstawia ustawienia pierwszej karty:

![Ustawienia karty](./media/tutorial-in-store-analytics-visualize-insights-pnp/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Dodawanie kafelków w celu wyświetlenia danych obłożenia wyewidencjonowywanych

Dodaj cztery kafelki kart, aby wyświetlić długość kolejki i czas przebywania dla dwóch transakcji w sklepie. Użyj informacji w poniższej tabeli, aby utworzyć kafelki. Aby dodać każdy kafelek, zacznij od wybrania **... (Więcej opcji) > Dodaj kafelek**. Wybierz **pozycję Niestandardowe dane przesyłania strumieniowego**, a następnie wybierz pozycję **Dalej:**

| Ustawienie | #1 karty | #2 karty | #3 karty | #4 karty |
| ------- | ------- | ------- | ------- | ------- |
| Dataset | Czujnik obłożenia | Czujnik obłożenia | Czujnik obłożenia | Czujnik obłożenia |
| Typ wizualizacji | Wykres kolumnowy grupowany | Wykres kolumnowy grupowany | Miernik | Miernik |
| Oś    | Znacznik czasu | Znacznik czasu | Nie dotyczy | Nie dotyczy |
| Wartość | Czas przebywania 1 | Czas przebywania 2 | Długość kolejki 1 | Długość kolejki 2 |
| Okno czasu | 60 min. | 60 min. |  Nie dotyczy | Nie dotyczy |
| Tytuł | Czas przebywania | Czas przebywania | Długość kolejki | Długość kolejki |
| Podtytuł | Kasa 1 | Kasa 2 | Kasa 1 | Kasa 2 |

Zmień rozmiar i zmień kolejność kafelków na pulpicie nawigacyjnym, aby wyglądały następująco:

![Pulpit nawigacyjny usługi Power BI](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard.png)

Można dodać kilka dodatkowych zasobów graficznych, aby jeszcze bardziej dostosować pulpit nawigacyjny:

![Pulpit nawigacyjny usługi Power BI](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli aplikacja IoT Central została ukończona, możesz ją usunąć, logując się do aplikacji i przechodząc do strony **Ustawienia aplikacji** w sekcji **Administracja.**

Jeśli chcesz zachować aplikację, ale zmniejszyć koszty skojarzone z nią, należy wyłączyć eksport danych, który wysyła dane telemetryczne do centrum zdarzeń.

Centrum zdarzeń i aplikację logiki można usunąć w portalu Azure, usuwając grupę zasobów o nazwie **retail-store-analysis**.

Zestawy danych i pulpit nawigacyjny usługi Power BI można usunąć, usuwając obszar roboczy ze strony ustawień usługi Power BI dla obszaru roboczego.

## <a name="next-steps"></a>Następne kroki

Te trzy samouczki pokazały kompleksowe rozwiązanie, które używa szablonu aplikacji **In-Store analytics — checkout** IoT Central. Podłączono urządzenia do aplikacji, użyto usługi IoT Central do monitorowania urządzeń i użyto usługi Power BI do utworzenia pulpitu nawigacyjnego w celu wyświetlenia szczegółowych informacji z danych telemetrycznych urządzenia. Zalecanym następnym krokiem jest zbadanie jednego z innych szablonów aplikacji IoT Central:

> [!div class="nextstepaction"]
> * [Tworzenie rozwiązań dla branży energetycznej za pomocą usługi IoT Central](../energy/overview-iot-central-energy.md)
> * [Tworzenie rozwiązań dla instytucji rządowych za pomocą usługi IoT Central](../government/overview-iot-central-government.md)
> * [Tworzenie rozwiązań dla opieki zdrowotnej za pomocą usługi IoT Central](../healthcare/overview-iot-central-healthcare.md)
