using System;
using System.Collections.Generic;
using System.Linq;

public class Program
{
    static void Main(string[] args)
    {
        TaxiService taxiService = new TaxiService();
        Location userLocation = new Location(37.7749, -122.4194); // Пример: Сан-Франциско

        Console.WriteLine("Поиск такси поблизости...");
        List<Taxi> nearbyTaxis = taxiService.FindNearbyTaxis(userLocation, 5); // Искать в радиусе 5 км

        if (nearbyTaxis.Any())
        {
            Console.WriteLine("Найденные такси:");
            foreach (var taxi in nearbyTaxis)
            {
                Console.WriteLine($"Такси ID: {taxi.Id}, Местоположение: ({taxi.Location.Latitude}, {taxi.Location.Longitude})");
            }
        }
        else
        {
            Console.WriteLine("Такси не найдены поблизости.");
        }
    }
}

public class TaxiService
{
    private List<Taxi> taxis;

    public TaxiService()
    {
        // Инициализация с некоторыми такси
        taxis = new List<Taxi>
        {
            new Taxi { Id = 1, Location = new Location(37.7749, -122.4194) }, // Такси в центре Сан-Франциско
            new Taxi { Id = 2, Location = new Location(37.7849, -122.4294) }, // Такси немного на юг
            new Taxi { Id = 3, Location = new Location(37.7649, -122.4394) }, // Такси немного на север
            new Taxi { Id = 4, Location = new Location(37.7949, -122.4094) }, // Такси на восток
            new Taxi { Id = 5, Location = new Location(37.7549, -122.4194) }  // Такси на западе
        };
    }

    public List<Taxi> FindNearbyTaxis(Location userLocation, double radiusInKm)
    {
        return taxis.Where(taxi => CalculateDistance(userLocation, taxi.Location) <= radiusInKm).ToList();
    }

    private double CalculateDistance(Location loc1, Location loc2)
    {
        // Простая формула для расчета расстояния между двумя точками
        double dLat = (loc2.Latitude - loc1.Latitude) * Math.PI / 180.0;
        double dLon = (loc2.Longitude - loc1.Longitude) * Math.PI / 180.0;
        double a = Math.Sin(dLat / 2) * Math.Sin(dLat / 2) +
                   Math.Cos(loc1.Latitude * Math.PI / 180.0) * Math.Cos(loc2.Latitude * Math.PI / 180.0) *
                   Math.Sin(dLon / 2) * Math.Sin(dLon / 2);
        double c = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
        double radius = 6371; // Радиус Земли в километрах
        return radius * c; // Расстояние в километрах
    }
}

public class Taxi
{
    public int Id { get; set; }
    public Location Location { get; set; }
}

public class Location
{
    public double Latitude { get; set; }
    public double Longitude { get; set; }

    public Location(double latitude, double longitude)
    {
        Latitude = latitude;
        Longitude = longitude;
    }
}
