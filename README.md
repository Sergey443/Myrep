# Myrep
def compare_dicts(dict1, dict2, exclude_keys=None):
    if exclude_keys is None:
        exclude_keys = set()

    differences = {}

    # Функция для рекурсивного сравнения
    def recursive_compare(d1, d2, path=''):
        for key in d1.keys():
            current_path = f"{path}.{key}" if path else key

            if key in exclude_keys:
                continue  # Пропускаем ключи, которые нужно исключить

            if key not in d2:
                differences[current_path] = {'status': 'missing_in_dict2', 'value': d1[key]}
                continue

            value1 = d1[key]
            value2 = d2[key]

            if isinstance(value1, dict) and isinstance(value2, dict):
                # Рекурсивно сравниваем вложенные словари
                recursive_compare(value1, value2, current_path)
            elif isinstance(value1, list) and isinstance(value2, list):
                # Сравниваем списки
                compare_lists(value1, value2, current_path)
            elif value1 != value2:
                differences[current_path] = {'status': 'different', 'value_dict1': value1, 'value_dict2': value2}

        for key in d2.keys():
            current_path = f"{path}.{key}" if path else key
            if key not in d1 and key not in exclude_keys:
                differences[current_path] = {'status': 'missing_in_dict1', 'value': d2[key]}

    def compare_lists(list1, list2, path):
        # Сравниваем списки по элементам
        for index in range(max(len(list1), len(list2))):
            current_path = f"{path}[{index}]"
            if index >= len(list1):
                differences[current_path] = {'status': 'missing_in_list1', 'value': list2[index]}
            elif index >= len(list2):
                differences[current_path] = {'status': 'missing_in_list2', 'value': list1[index]}
            else:
                value1 = list1[index]
                value2 = list2[index]
                
                if isinstance(value1, dict) and isinstance(value2, dict):
                    recursive_compare(value1, value2, current_path)
                elif value1 != value2:
                    differences[current_path] = {'status': 'different', 'value_list1': value1, 'value_list2': value2}

    # Запускаем рекурсивное сравнение
    recursive_compare(dict1, dict2)

    return differences

# Примеры словарей для сравнения
dict1 = {
    "запрос": "сгенерируй баннер с заголовком 'Будущее начинается здесь!' и текстом 'Откройте новые возможности вместе с нами! Уникальные предложения, инновационные решения и вдохновение для вашего успеха. Действуйте сегодня — завтра начинается сейчас!'",
    "код": {
        "id": "b5764cbf-d23b-4d2c-a502-f6398bf01b42",
        "screenId": "aa14b9ae-e162-47d8-92ee-63096178fb80",
        "name": "edgar_test4",
        "description": None,
        "status": "DRAFT",
        "version": 1,
        "revision": 1,
        "bodyQueries": [],
        "bodyStates": [
            {
                "state": "content",
                "block": {
                    "type": "screen_normal",
                    "blockId": "aa14b9ae-e162-47d8-92ee-63096178fb80",
                    "blocks": [
                        {
                            "type": "root",
                            "blockId": "62a7f880-2bef-1237-93fa-fb825780565b",
                            "blocks": [
                                {
                                    "type": "list",
                                    "blockId": "bb2dc489-2bf4-1237-93fa-fb825780565b",
                                    "blocks": [
                                        {
                                            "type": "primary-banner",
                                            "blockId": "4c089e07-2bfd-1237-93fa-fb825780565b",
                                            "blocks": []
                                        }
                                    ],
                                    "position": "body"
                                }
                            ]
                        }
                    ]
                },
                # ... остальная часть словаря ...
            }
        ],
        # ... остальная часть словаря ...
    }
}

dict2 = {
    "запрос": "сгенерируй баннер с заголовком 'Будущее начинается здесь!' и текстом 'Откройте новые возможности вместе с нами! Уникальные предложения, инновационные решения и вдохновение для вашего успеха. Действуйте сегодня — завтра начинается сейчас!'",
    "код": {
        "id": "38c6cbfa-5b9e-4f03-8036-68318bd5d610",
        # ... остальная часть словаря ...
    }
}

# Сравнение словарей
differences = compare_dicts(dict1, dict2)

# Вывод результатов
if differences:
    print("Найдены различия:")
    for path, diff in differences.items():
        print(f"{path}: {diff}")
else:
    print("Словари идентичны.")
