import { useState, useEffect } from 'react';
import { Input } from "@/components/ui/input";
import { Card, CardContent } from "@/components/ui/card";
import { Tabs, TabsList, TabsTrigger, TabsContent } from "@/components/ui/tabs";

export default function ConverterApp() {
  const [weightKg, setWeightKg] = useState(0);
  const [amount, setAmount] = useState(0);
  const [fromCurrency, setFromCurrency] = useState('USD');
  const [toCurrency, setToCurrency] = useState('EUR');
  const [rates, setRates] = useState({});
  const [hours, setHours] = useState(0);
  const [celsius, setCelsius] = useState(0);
  const [mph, setMph] = useState(0);
  const [areaSqM, setAreaSqM] = useState(0);
  const [volumeL, setVolumeL] = useState(0);

  const allTimezones = [
    { city: 'New York', offset: -4 },
    { city: 'London', offset: 1 },
    { city: 'Tokyo', offset: 9 },
    { city: 'Sydney', offset: 10 },
    { city: 'Los Angeles', offset: -7 }
  ];

  useEffect(() => {
    fetch('https://api.exchangerate-api.com/v4/latest/USD')
      .then(response => response.json())
      .then(data => {
        setRates(data.rates);
      });
  }, []);

  const convertWeight = (kg) => (kg * 2.20462).toFixed(2);
  const convertCurrency = (amount, from, to) => {
    if (!rates[from] || !rates[to]) return '...';
    return (amount / rates[from] * rates[to]).toFixed(2);
  };
  const getLocalTimes = () => {
    const utc = new Date().getUTCHours();
    return allTimezones.map(tz => {
      let localHour = (utc + tz.offset + 24) % 24;
      return `${tz.city}: ${localHour}:00`;
    });
  };
  const convertTemperature = (celsius) => (celsius * 9/5 + 32).toFixed(2);
  const convertSpeed = (mph) => {
    const conversions = {
      "km/h": (mph * 1.60934).toFixed(2),
      "m/s": (mph * 0.44704).toFixed(2),
      "knots": (mph * 0.868976).toFixed(2)
    };
    return conversions;
  };
  const convertArea = (sqm) => {
    return {
      "sq ft": (sqm * 10.7639).toFixed(2),
      "acres": (sqm * 0.000247105).toFixed(4),
      "hectares": (sqm * 0.0001).toFixed(4)
    };
  };
  const convertVolume = (liters) => {
    return {
      "gallons (US)": (liters * 0.264172).toFixed(3),
      "milliliters": (liters * 1000).toFixed(1),
      "cubic meters": (liters * 0.001).toFixed(3)
    };
  };

  return (
    <div className="p-6 max-w-3xl mx-auto bg-black text-white" style={{ fontFamily: 'Monospace' }}>
      <h1 className="text-3xl font-bold mb-4 text-white">Universal Converter</h1>
      <Tabs defaultValue="weight">
        <TabsList className="bg-white text-black">
          <TabsTrigger value="weight">Weight</TabsTrigger>
          <TabsTrigger value="currency">Currency</TabsTrigger>
          <TabsTrigger value="time">Time</TabsTrigger>
          <TabsTrigger value="temperature">Temperature</TabsTrigger>
          <TabsTrigger value="speed">Speed</TabsTrigger>
          <TabsTrigger value="area">Area</TabsTrigger>
          <TabsTrigger value="volume">Volume</TabsTrigger>
        </TabsList>

        <TabsContent value="weight">
          <Card className="bg-[#0a0a0a] border border-white">
            <CardContent className="p-4">
              <label className="block mb-2 font-semibold text-white">Kilograms</label>
              <Input
                type="number"
                value={weightKg}
                onChange={(e) => setWeightKg(e.target.value)}
                placeholder="Enter weight in kg"
              />
              <p className="mt-4 text-white">Pounds: <strong>{convertWeight(weightKg)}</strong></p>
            </CardContent>
          </Card>
        </TabsContent>

        <TabsContent value="currency">
          <Card className="bg-[#0a0a0a] border border-white">
            <CardContent className="p-4">
              <label className="block mb-2 font-semibold text-white">Amount</label>
              <Input type="number" value={amount} onChange={(e) => setAmount(e.target.value)} />
              <div className="flex gap-4 mt-4">
                <select value={fromCurrency} onChange={(e) => setFromCurrency(e.target.value)} className="text-black">
                  {Object.keys(rates).map(code => <option key={code}>{code}</option>)}
                </select>
                <span className="text-white">to</span>
                <select value={toCurrency} onChange={(e) => setToCurrency(e.target.value)} className="text-black">
                  {Object.keys(rates).map(code => <option key={code}>{code}</option>)}
                </select>
              </div>
              <p className="mt-4 text-white">Converted: <strong>{convertCurrency(amount, fromCurrency, toCurrency)}</strong></p>
            </CardContent>
          </Card>
        </TabsContent>

        <TabsContent value="time">
          <Card className="bg-[#0a0a0a] border border-white">
            <CardContent className="p-4">
              <h2 className="text-xl font-semibold mb-2 text-white">Current Time in Major Cities</h2>
              {getLocalTimes().map((line, index) => (
                <p key={index} className="text-white">{line}</p>
              ))}
            </CardContent>
          </Card>
        </TabsContent>

        <TabsContent value="temperature">
          <Card className="bg-[#0a0a0a] border border-white">
            <CardContent className="p-4">
              <label className="block mb-2 font-semibold text-white">Celsius</label>
              <Input
                type="number"
                value={celsius}
                onChange={(e) => setCelsius(e.target.value)}
                placeholder="Enter temperature in °C"
              />
              <p className="mt-4 text-white">Fahrenheit: <strong>{convertTemperature(celsius)}</strong></p>
            </CardContent>
          </Card>
        </TabsContent>

        <TabsContent value="speed">
          <Card className="bg-[#0a0a0a] border border-white">
            <CardContent className="p-4">
              <label className="block mb-2 font-semibold text-white">Miles per Hour (mph)</label>
              <Input
                type="number"
                value={mph}
                onChange={(e) => setMph(e.target.value)}
                placeholder="Enter speed in mph"
              />
              <p className="mt-4 text-white">km/h: <strong>{convertSpeed(mph)['km/h']}</strong></p>
              <p className="text-white">m/s: <strong>{convertSpeed(mph)['m/s']}</strong></p>
              <p className="text-white">knots: <strong>{convertSpeed(mph)['knots']}</strong></p>
            </CardContent>
          </Card>
        </TabsContent>

        <TabsContent value="area">
          <Card className="bg-[#0a0a0a] border border-white">
            <CardContent className="p-4">
              <label className="block mb-2 font-semibold text-white">Square Meters (m²)</label>
              <Input
                type="number"
                value={areaSqM}
                onChange={(e) => setAreaSqM(e.target.value)}
                placeholder="Enter area in m²"
              />
              <p className="mt-4 text-white">sq ft: <strong>{convertArea(areaSqM)["sq ft"]}</strong></p>
              <p className="text-white">acres: <strong>{convertArea(areaSqM)["acres"]}</strong></p>
              <p className="text-white">hectares: <strong>{convertArea(areaSqM)["hectares"]}</strong></p>
            </CardContent>
          </Card>
        </TabsContent>

        <TabsContent value="volume">
          <Card className="bg-[#0a0a0a] border border-white">
            <CardContent className="p-4">
              <label className="block mb-2 font-semibold text-white">Liters (L)</label>
              <Input
                type="number"
                value={volumeL}
                onChange={(e) => setVolumeL(e.target.value)}
                placeholder="Enter volume in liters"
              />
              <p className="mt-4 text-white">gallons (US): <strong>{convertVolume(volumeL)["gallons (US)"]}</strong></p>
              <p className="text-white">milliliters: <strong>{convertVolume(volumeL)["milliliters"]}</strong></p>
              <p className="text-white">cubic meters: <strong>{convertVolume(volumeL)["cubic meters"]}</strong></p>
            </CardContent>
          </Card>
        </TabsContent>

      </Tabs>
    </div>
  );
}
