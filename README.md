int zero = 0;  // Глобальная переменная для хранения нулевого значения
unsigned long previousMillis = 0;  // Переменная для хранения времени последнего измерения
const long interval = 100;  // Интервал между измерениями (1 секунда)

// Функция для усреднения значений аналогового входа
int averageAnalogRead(int pin, int numSamples) {
  long sum = 0;
  for (int i = 0; i < numSamples; i++) {
    sum += analogRead(pin);
    delay(10);  // Небольшая задержка между измерениями
  }
  return sum / numSamples;
}

void setup() {
  Serial.begin(9600);  // Инициализация последовательного соединения
  
  // Усредняем начальное значение потенциометра
  zero = averageAnalogRead(A0, 10);  // Усредняем 10 измерений
  Serial.println("Zero point set to: " + String(zero));  // Для отладки
}

void loop() {
  unsigned long currentMillis = millis();  // Текущее время

  // Проверяем, прошло ли нужное время (1 секунда)
  if (currentMillis - previousMillis >= interval) {
    // Сохраняем время этого измерения
    previousMillis = currentMillis;

    int rawValue = analogRead(A0);  // Считываем текущее значение потенциометра
    int calibratedValue = rawValue - zero;  // Вычитаем нулевое значение

    // Применяем функцию map к скорректированному значению
    int mappedValue = map(calibratedValue, 0, 1023 - zero, 0, 1023);
  
    // Ограничиваем значения в диапазоне от 0 до 1023
    mappedValue = constrain(mappedValue, 0, 1023);
  
    Serial.println(mappedValue);  // Выводим масштабированное значение
  }
}
