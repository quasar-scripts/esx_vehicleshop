# esx_vehicleshop
This version of esx_vehicleshop has been modified to work with qs-casino.

![Image of Notification](https://i.imgur.com/NUOA0Us.png)

## Modifications


Add in esx_vehicleshop/client/main.lua


```lua
--- LUCKYWHEEL
RegisterNetEvent("qs-luckywheel:winCar")
AddEventHandler("qs-luckywheel:winCar", function() 
    
    ESX.Game.SpawnVehicle("emerus", { x = 933.29 , y = -2.82 , z = 78.76 }, 144.6, function (vehicle) -- Vehicle name, emerus default vehicle.
		local playerPed = PlayerPedId()
		TaskWarpPedIntoVehicle(playerPed, vehicle, -1)

        local newPlate     = GeneratePlate()
        local vehicleProps = ESX.Game.GetVehicleProperties(vehicle)
        vehicleProps.plate = newPlate
        SetVehicleNumberPlateText(vehicle, newPlate)

        TriggerServerEvent('esx_vehicleshop:setVehicleOwned', vehicleProps)

        ESX.ShowNotification("You won a supercar!")
    
	end)

    FreezeEntityPosition(playerPed, false)
    SetEntityVisible(playerPed, true)

end)
```

Add in esx_vehicleshop/server/main.lua

```lua
RegisterServerEvent('esx_vehicleshop:setVehicleOwned')
AddEventHandler('esx_vehicleshop:setVehicleOwned', function (vehicleProps)
	local _source = source
	local xPlayer = ESX.GetPlayerFromId(_source)
		MySQL.Async.execute('INSERT INTO owned_vehicles (owner, plate, vehicle) VALUES (@Owner, @plate, @vehicle)',{
		['@Owner'] = xPlayer.identifier,
		['@plate'] = vehicleProps.plate,
		['@vehicle'] = json.encode(vehicleProps)
		}, function (rowsChanged)
	end)
end)
```
