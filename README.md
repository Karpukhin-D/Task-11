# Практическая работа №11: Наследование в C#, модификаторы доступа, sealed, операторы is и as
## Выполнил студент группы П25-2.1 Карпухин Дмитрий
---

<picture>
  <img src="https://github.com/Karpukhin-D/Task-11/blob/main/screens/1.png">
</picture>

<picture>
  <img src="https://github.com/Karpukhin-D/Task-11/blob/main/screens/2.png">
</picture>

```csharp

using System;

class Transport
{
    protected string TransportId;
    protected int CurrentSpeed;
    private double _mileageKm;

    public string Id => TransportId;
    public double Mileage => _mileageKm;

    public int Speed
    {
        get => CurrentSpeed;
        protected set => CurrentSpeed = Math.Max(0, value);
    }

    public Transport(string id, double mileage)
    {
        TransportId = id;
        _mileageKm = mileage;
        Speed = 0;
    }

    public virtual void Move(double distanceKm)
    {
        if (distanceKm < 0)
        {
            Console.WriteLine("Расстояние не может быть отрицательным.");
            return;
        }

        _mileageKm += distanceKm;
        Console.WriteLine($"{Id}: пройдено {distanceKm} км.");
    }

    public virtual void DisplayInfo()
    {
        Console.WriteLine($"Номер: {Id}");
        Console.WriteLine($"Скорость: {Speed} км/ч");
        Console.WriteLine($"Пробег: {Mileage} км");
    }
}

class PublicBus : Transport
{
    public int PassengerCapacity { get; }
    public int CurrentPassengers { get; private set; }

    public PublicBus(string id, double mileage, int passengerCapacity)
        : base(id, mileage)
    {
        PassengerCapacity = passengerCapacity;
        CurrentPassengers = 0;
    }

    public void BoardPassengers(int count)
    {
        if (count <= 0)
        {
            Console.WriteLine("Количество пассажиров должно быть положительным.");
        }
        else if (CurrentPassengers + count <= PassengerCapacity)
        {
            CurrentPassengers += count;
            Console.WriteLine($"В автобус сели {count} пассажиров.");
        }
        else
        {
            Console.WriteLine("Недостаточно свободных мест в автобусе.");
        }
    }

    public override void Move(double distanceKm)
    {
        base.Move(distanceKm);
        Console.WriteLine("Автобус следует по городскому маршруту с остановками.");
    }
}

sealed class ElectricScooter : Transport
{
    private int _batteryPercent;

    public int BatteryPercent => _batteryPercent;

    public ElectricScooter(string id, double mileage, int batteryPercent)
        : base(id, mileage)
    {
        if (batteryPercent < 0)
            _batteryPercent = 0;
        else if (batteryPercent > 100)
            _batteryPercent = 100;
        else
            _batteryPercent = batteryPercent;
    }

    public void Recharge()
    {
        _batteryPercent = 100;
        Console.WriteLine($"{Id}: аккумулятор заряжен до 100%.");
    }

    public override void Move(double distanceKm)
    {
        if (distanceKm < 0)
        {
            Console.WriteLine("Расстояние не может быть отрицательным.");
            return;
        }

        int batteryNeeded = (int)Math.Ceiling(distanceKm * 2);

        if (_batteryPercent < batteryNeeded)
        {
            Console.WriteLine($"{Id}: недостаточно заряда для движения.");
            return;
        }

        _batteryPercent -= batteryNeeded;
        base.Move(distanceKm);
        Console.WriteLine($"Остаток заряда: {_batteryPercent}%.");
    }
}

class CargoTruck : Transport
{
    public double MaxCargoTons { get; }
    public bool HasTrailer { get; set; }

    public CargoTruck(string id, double mileage, double maxCargoTons, bool hasTrailer)
        : base(id, mileage)
    {
        MaxCargoTons = maxCargoTons;
        HasTrailer = hasTrailer;
    }

    public override void DisplayInfo()
    {
        base.DisplayInfo();
        Console.WriteLine($"Грузоподъемность: {MaxCargoTons} т");
        Console.WriteLine($"Прицеп: {(HasTrailer ? "есть" : "нет")}");
    }

    public void UnloadCargo()
    {
        Console.WriteLine($"{Id}: груз выгружен.");
    }
}

class Program
{
    static void Main()
    {
        Console.WriteLine("=== ТРАНСПОРТНЫЙ ХАБ МЕГАПОЛИСА ===");
        Console.WriteLine();

        Transport[] fleet = new Transport[4];

        fleet[0] = new PublicBus("BUS-101", 12000, 40);
        fleet[1] = new ElectricScooter("SCOOTER-201", 350, 15);
        fleet[2] = new ElectricScooter("SCOOTER-202", 180, 80);
        fleet[3] = new CargoTruck("TRUCK-301", 25000, 20, true);

        Console.WriteLine("=== ПОСАДКА ПАССАЖИРОВ ===");
        PublicBus bus = fleet[0] as PublicBus;

        if (bus != null)
        {
            bus.BoardPassengers(25);
        }

        Console.WriteLine();
        Console.WriteLine("=== ДВИЖЕНИЕ ТРАНСПОРТА ===");

        foreach (Transport item in fleet)
        {
            item.Move(10.0);
            Console.WriteLine();
        }

        Console.WriteLine("=== ПРОВЕРКА ЗАРЯДА САМОКАТОВ ===");

        foreach (Transport item in fleet)
        {
            if (item is ElectricScooter scooter)
            {
                Console.WriteLine($"{scooter.Id}: заряд {scooter.BatteryPercent}%.");

                if (scooter.BatteryPercent < 20)
                {
                    scooter.Recharge();
                }
            }
        }

        Console.WriteLine();
        Console.WriteLine("=== ВЫГРУЗКА ГРУЗА ===");

        foreach (Transport item in fleet)
        {
            CargoTruck truck = item as CargoTruck;

            if (truck != null)
            {
                truck.UnloadCargo();
            }
        }

        Console.WriteLine();
        Console.WriteLine("=== ИНФОРМАЦИЯ ОБ АВТОПАРКЕ ===");

        foreach (Transport item in fleet)
        {
            item.DisplayInfo();
            Console.WriteLine();
        }

        Console.WriteLine("=== ЗАВЕРШЕНИЕ ПРОГРАММЫ ===");
    }
}
```
