// VolumeDiscountDemo.cpp
// Полный код для Visual Studio в одном файле

#include <iostream>
#include <vector>
#include <memory>
#include <stdexcept>
#include <string>
#include <iomanip>

// ==================== КЛАСС VolumeDiscount ====================
class VolumeDiscount {
private:
    int threshold;     // Пороговое количество абонентов для скидки
    double discount;   // Размер скидки (в долях от 0 до 1)

public:
    // Конструктор с параметрами
    VolumeDiscount(int thresh = 100, double disc = 0.1) 
        : threshold(thresh), discount(disc) 
    {
        validateParameters();
    }

    // Вычисление стоимости с учетом скидки за объем
    double compute(double tariff, int subscribers) const {
        validateInput(tariff, subscribers);
        
        double baseCost = tariff * subscribers;
        
        if (subscribers > threshold) {
            double discountedCost = baseCost * (1.0 - discount);
            return discountedCost;
        }
        
        return baseCost;
    }

    // Геттеры
    int getThreshold() const { return threshold; }
    double getDiscount() const { return discount; }
    double getDiscountPercent() const { return discount * 100.0; }

    // Сеттеры с валидацией
    void setThreshold(int thresh) {
        if (thresh <= 0) {
            throw std::invalid_argument("Порог должен быть положительным числом");
        }
        threshold = thresh;
    }

    void setDiscount(double disc) {
        if (disc <= 0.0 || disc >= 1.0) {
            throw std::invalid_argument("Скидка должна быть в диапазоне от 0 до 1 (например, 0.15 = 15%)");
        }
        discount = disc;
    }

    // Получение информации о скидке
    std::string getInfo() const {
        return "Объемная скидка: " + std::to_string(static_cast<int>(discount * 100)) + 
               "% при количестве абонентов > " + std::to_string(threshold);
    }

private:
    // Валидация параметров при создании
    void validateParameters() {
        if (threshold <= 0) {
            throw std::invalid_argument("Порог должен быть положительным числом");
        }
        if (discount <= 0.0 || discount >= 1.0) {
            throw std::invalid_argument("Скидка должна быть в диапазоне от 0 до 1");
        }
    }

    // Валидация входных данных для вычисления
    void validateInput(double tariff, int subscribers) const {
        if (tariff <= 0) {
            throw std::invalid_argument("Тариф должен быть положительным числом");
        }
        if (subscribers < 0) {
            throw std::invalid_argument("Количество абонентов не может быть отрицательным");
        }
    }
};

// ==================== ФУНКЦИИ ДЛЯ ДЕМОНСТРАЦИИ ====================

// Функция для вывода таблицы с результатами
void printResultsTable(const VolumeDiscount& discountPolicy, double tariff, 
                       const std::vector<int>& subscribersList) {
    std::cout << std::string(60, '=') << "\n";
    std::cout << "РАСЧЕТ СТОИМОСТИ С ОБЪЕМНОЙ СКИДКОЙ\n";
    std::cout << std::string(60, '=') << "\n";
    std::cout << discountPolicy.getInfo() << "\n";
    std::cout << "Тариф за абонента: " << std::fixed << std::setprecision(2) << tariff << " руб.\n";
    std::cout << std::string(60, '-') << "\n";
    
    std::cout << std::left << std::setw(15) << "Абонентов" 
              << std::setw(20) << "Базовая стоимость" 
              << std::setw(15) << "Скидка" 
              << std::setw(20) << "Итоговая стоимость" 
              << "\n";
    std::cout << std::string(60, '-') << "\n";

    for (int subs : subscribersList) {
        double baseCost = tariff * subs;
        double finalCost = discountPolicy.compute(tariff, subs);
        bool hasDiscount = (subs > discountPolicy.getThreshold());
        
        std::cout << std::left << std::setw(15) << subs
                  << std::setw(20) << std::fixed << std::setprecision(2) << baseCost;
        
        if (hasDiscount) {
            std::cout << std::setw(15) << (std::to_string(static_cast<int>(discountPolicy.getDiscountPercent())) + "%");
        }
        else {
            std::cout << std::setw(15) << "0%";
        }
        
        std::cout << std::setw(20) << finalCost;
        
        if (hasDiscount) {
            double saved = baseCost - finalCost;
            std::cout << " (экономия: " << saved << " руб.)";
        }
        std::cout << "\n";
    }
    std::cout << std::string(60, '=') << "\n\n";
}

// Функция демонстрации разных сценариев
void demonstrateScenarios() {
    std::cout << "=== ДЕМОНСТРАЦИЯ РАЗНЫХ СЦЕНАРИЕВ ===\n\n";

    // Сценарий 1: Малое предприятие
    {
        std::cout << "СЦЕНАРИЙ 1: Малое предприятие\n";
        VolumeDiscount discount1(30, 0.1); // Порог 30, скидка 10%
        double tariff1 = 150.0;
        std::vector<int> subs1 = {15, 25, 30, 35, 40, 50};
        printResultsTable(discount1, tariff1, subs1);
    }

    // Сценарий 2: Средняя компания
    {
        std::cout << "СЦЕНАРИЙ 2: Средняя компания\n";
        VolumeDiscount discount2(100, 0.15); // Порог 100, скидка 15%
        double tariff2 = 120.0;
        std::vector<int> subs2 = {50, 75, 100, 125, 150, 200};
        printResultsTable(discount2, tariff2, subs2);
    }

    // Сценарий 3: Крупная корпорация
    {
        std::cout << "СЦЕНАРИЙ 3: Крупная корпорация\n";
        VolumeDiscount discount3(500, 0.2); // Порог 500, скидка 20%
        double tariff3 = 100.0;
        std::vector<int> subs3 = {200, 400, 500, 600, 800, 1000};
        printResultsTable(discount3, tariff3, subs3);
    }
}

// Функция для интерактивного тестирования
void interactiveTest() {
    std::cout << "=== ИНТЕРАКТИВНОЕ ТЕСТИРОВАНИЕ ===\n\n";
    
    try {
        int threshold;
        double discount, tariff;
        
        std::cout << "Введите параметры объемной скидки:\n";
        std::cout << "Пороговое количество абонентов: ";
        std::cin >> threshold;
        
        std::cout << "Размер скидки (например, 0.15 для 15%): ";
        std::cin >> discount;
        
        std::cout << "Тариф за абонента (руб.): ";
        std::cin >> tariff;
        
        VolumeDiscount userDiscount(threshold, discount);
        
        char continueInput = 'y';
        while (continueInput == 'y' || continueInput == 'Y') {
            int subscribers;
            std::cout << "\nВведите количество абонентов: ";
            std::cin >> subscribers;
            
            double cost = userDiscount.compute(tariff, subscribers);
            std::cout << "Итоговая стоимость: " << std::fixed << std::setprecision(2) 
                     << cost << " руб.\n";
            
            if (subscribers > threshold) {
                double baseCost = tariff * subscribers;
                double saved = baseCost - cost;
                std::cout << "Применена скидка " << (discount * 100) 
                         << "%, экономия: " << saved << " руб.\n";
            }
            
            std::cout << "\nПродолжить? (y/n): ";
            std::cin >> continueInput;
        }
    }
    catch (const std::exception& e) {
        std::cerr << "Ошибка: " << e.what() << "\n";
    }
}

// ==================== ГЛАВНАЯ ФУНКЦИЯ ====================
int main() {
    // Настройка вывода на русском (для Windows)
    setlocale(LC_ALL, "Russian");
    
    std::cout << "ПРОГРАММА ДЛЯ РАСЧЕТА ОБЪЕМНЫХ СКИДОК\n";
    std::cout << "======================================\n\n";
    
    int choice = 0;
    
    do {
        std::cout << "МЕНЮ:\n";
        std::cout << "1. Демонстрация разных сценариев\n";
        std::cout << "2. Интерактивное тестирование\n";
        std::cout << "3. Тестирование с фиксированными данными\n";
        std::cout << "0. Выход\n";
        std::cout << "Выберите действие: ";
        std::cin >> choice;
        
        switch (choice) {
            case 1:
                demonstrateScenarios();
                break;
                
            case 2:
                interactiveTest();
                break;
                
            case 3: {
                // Фиксированный тест
                std::cout << "\n=== ТЕСТ С ФИКСИРОВАННЫМИ ДАННЫМИ ===\n";
                VolumeDiscount testDiscount(50, 0.12);
                double testTariff = 200.0;
                std::vector<int> testSubs = {10, 25, 50, 60, 75, 100};
                printResultsTable(testDiscount, testTariff, testSubs);
                break;
            }
                
            case 0:
                std::cout << "Выход из программы...\n";
                break;
                
            default:
                std::cout << "Неверный выбор! Попробуйте снова.\n";
        }
        
        std::cout << "\n";
    } while (choice != 0);
    
    return 0;
}
