
### <a name="cacheskuname"></a>cacheSKUName
Warstwa cenowa nowej pamięci podręcznej Redis Azure.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Redis Cache."
      }
    },

Szablon definiuje wartości, które są dozwolone dla tego parametru (Basic lub Standard), a następnie przypisuje wartość domyślną (Basic), jeśli nie określono wartości. Basic zapewnia jeden węzeł o różnych rozmiarach dostępne w górę do 53 GB.
Standard udostępnia dwa węzły podstawowego/repliki o różnych rozmiarach dostępne w górę do umowy SLA 53 GB i 99,9%.

### <a name="cacheskufamily"></a>cacheSKUFamily
Rodzina dla jednostki sku.

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
Rozmiar nowego wystąpienia pamięci podręcznej Redis Azure. 

    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Redis Cache instance. "
      }
    }


Szablon definiuje wartości, które są dozwolone dla tego parametru (0, 1, 2, 3, 4, 5 lub 6) i przypisuje wartość domyślną (0), jeśli nie określono wartości. Te numery odpowiadają następujących rozmiarów pamięci podręcznej: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

