# WattsUp
Electricity boards and industries still rely on manual meter readings. This leads to:  Delayed or wrong readings  No real-time visibility  High wastage and inefficiency  My project will automatically read consumption data, send it to the cloud, and use AI to predict usage and detect anomalies.



import React, { useState, useEffect } from 'react';
import { BarChart, Bar, PieChart, Pie, Cell, LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from 'recharts';
import { Zap, Plus, Trash2, Power, DollarSign } from 'lucide-react';

const EnergyDashboard = () => {
  const [appliances, setAppliances] = useState([
    { id: 1, name: 'Refrigerator', watts: 150, hours: 24, active: true },
    { id: 2, name: 'Air Conditioner', watts: 1500, hours: 8, active: true },
    { id: 3, name: 'LED TV', watts: 100, hours: 6, active: true },
    { id: 4, name: 'Washing Machine', watts: 500, hours: 1, active: false }
  ]);
  
  const [newAppliance, setNewAppliance] = useState({ name: '', watts: '', hours: '' });
  const [costPerKwh, setCostPerKwh] = useState(8);

  const calculateDailyKwh = (watts, hours) => (watts * hours) / 1000;
  
  const totalDailyConsumption = appliances
    .filter(a => a.active)
    .reduce((sum, a) => sum + calculateDailyKwh(a.watts, a.hours), 0);
  
  const totalMonthlyCost = totalDailyConsumption * 30 * costPerKwh;
  const totalActiveWatts = appliances.filter(a => a.active).reduce((sum, a) => sum + a.watts, 0);

  const addAppliance = () => {
    if (newAppliance.name && newAppliance.watts && newAppliance.hours) {
      setAppliances([...appliances, {
        id: Date.now(),
        name: newAppliance.name,
        watts: parseFloat(newAppliance.watts),
        hours: parseFloat(newAppliance.hours),
        active: true
      }]);
      setNewAppliance({ name: '', watts: '', hours: '' });
    }
  };

  const deleteAppliance = (id) => {
    setAppliances(appliances.filter(a => a.id !== id));
  };

  const toggleAppliance = (id) => {
    setAppliances(appliances.map(a => 
      a.id === id ? { ...a, active: !a.active } : a
    ));
  };

  const chartData = appliances.map(a => ({
    name: a.name,
    dailyKwh: calculateDailyKwh(a.watts, a.hours),
    watts: a.watts,
    active: a.active
  })).filter(a => a.active);

  const COLORS = ['#3b82f6', '#10b981', '#f59e0b', '#ef4444', '#8b5cf6', '#ec4899'];

  return (
    <div className="min-h-screen bg-gradient-to-br from-slate-900 via-blue-900 to-slate-900 p-6">
      <div className="max-w-7xl mx-auto">
        {/* Header */}
        <div className="text-center mb-8">
          <div className="flex items-center justify-center gap-3 mb-2">
            <Zap className="w-12 h-12 text-yellow-400" />
            <h1 className="text-4xl font-bold text-white">WattsUp</h1>
          </div>
          <p className="text-blue-200">Track and visualize your home energy consumption</p>
        </div>

        {/* Stats Cards */}
        <div className="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
          <div className="bg-white/10 backdrop-blur-lg rounded-xl p-6 border border-white/20">
            <div className="flex items-center gap-3 mb-2">
              <Power className="w-8 h-8 text-blue-400" />
              <h3 className="text-lg font-semibold text-white">Active Power</h3>
            </div>
            <p className="text-3xl font-bold text-blue-300">{totalActiveWatts.toFixed(0)} W</p>
          </div>
          
          <div className="bg-white/10 backdrop-blur-lg rounded-xl p-6 border border-white/20">
            <div className="flex items-center gap-3 mb-2">
              <Zap className="w-8 h-8 text-yellow-400" />
              <h3 className="text-lg font-semibold text-white">Daily Usage</h3>
            </div>
            <p className="text-3xl font-bold text-yellow-300">{totalDailyConsumption.toFixed(2)} kWh</p>
          </div>
          
          <div className="bg-white/10 backdrop-blur-lg rounded-xl p-6 border border-white/20">
            <div className="flex items-center gap-3 mb-2">
              <DollarSign className="w-8 h-8 text-green-400" />
              <h3 className="text-lg font-semibold text-white">Monthly Cost</h3>
            </div>
            <p className="text-3xl font-bold text-green-300">₹{totalMonthlyCost.toFixed(2)}</p>
          </div>
        </div>

        {/* Add Appliance Form */}
        <div className="bg-white/10 backdrop-blur-lg rounded-xl p-6 mb-8 border border-white/20">
          <h2 className="text-2xl font-bold text-white mb-4">Add New Appliance</h2>
          <div className="grid grid-cols-1 md:grid-cols-4 gap-4">
            <input
              type="text"
              placeholder="Appliance Name"
              value={newAppliance.name}
              onChange={(e) => setNewAppliance({...newAppliance, name: e.target.value})}
              className="px-4 py-2 rounded-lg bg-white/20 text-white placeholder-blue-200 border border-white/30 focus:outline-none focus:border-blue-400"
            />
            <input
              type="number"
              placeholder="Watts"
              value={newAppliance.watts}
              onChange={(e) => setNewAppliance({...newAppliance, watts: e.target.value})}
              className="px-4 py-2 rounded-lg bg-white/20 text-white placeholder-blue-200 border border-white/30 focus:outline-none focus:border-blue-400"
            />
            <input
              type="number"
              placeholder="Hours/Day"
              value={newAppliance.hours}
              onChange={(e) => setNewAppliance({...newAppliance, hours: e.target.value})}
              className="px-4 py-2 rounded-lg bg-white/20 text-white placeholder-blue-200 border border-white/30 focus:outline-none focus:border-blue-400"
            />
            <button
              onClick={addAppliance}
              className="px-6 py-2 bg-blue-500 hover:bg-blue-600 text-white rounded-lg font-semibold flex items-center justify-center gap-2 transition-colors"
            >
              <Plus className="w-5 h-5" />
              Add
            </button>
          </div>
          <div className="mt-4">
            <label className="text-white flex items-center gap-2">
              <span>Cost per kWh: ₹</span>
              <input
                type="number"
                step="0.5"
                value={costPerKwh}
                onChange={(e) => setCostPerKwh(parseFloat(e.target.value))}
                className="w-24 px-3 py-1 rounded-lg bg-white/20 text-white border border-white/30 focus:outline-none focus:border-blue-400"
              />
            </label>
          </div>
        </div>

        {/* Appliances List */}
        <div className="bg-white/10 backdrop-blur-lg rounded-xl p-6 mb-8 border border-white/20">
          <h2 className="text-2xl font-bold text-white mb-4">Appliances</h2>
          <div className="space-y-3">
            {appliances.map((appliance) => (
              <div
                key={appliance.id}
                className={`flex items-center justify-between p-4 rounded-lg ${
                  appliance.active ? 'bg-green-500/20 border-green-400/50' : 'bg-red-500/20 border-red-400/50'
                } border`}
              >
                <div className="flex items-center gap-4 flex-1">
                  <button
                    onClick={() => toggleAppliance(appliance.id)}
                    className={`w-12 h-12 rounded-full flex items-center justify-center ${
                      appliance.active ? 'bg-green-500' : 'bg-red-500'
                    }`}
                  >
                    <Power className="w-6 h-6 text-white" />
                  </button>
                  <div>
                    <h3 className="text-lg font-semibold text-white">{appliance.name}</h3>
                    <p className="text-blue-200">
                      {appliance.watts}W • {appliance.hours}h/day • {calculateDailyKwh(appliance.watts, appliance.hours).toFixed(2)} kWh/day
                    </p>
                  </div>
                </div>
                <button
                  onClick={() => deleteAppliance(appliance.id)}
                  className="p-2 hover:bg-red-500/30 rounded-lg transition-colors"
                >
                  <Trash2 className="w-5 h-5 text-red-400" />
                </button>
              </div>
            ))}
          </div>
        </div>

        {/* Charts */}
        <div className="grid grid-cols-1 lg:grid-cols-2 gap-8">
          {/* Bar Chart */}
          <div className="bg-white/10 backdrop-blur-lg rounded-xl p-6 border border-white/20">
            <h2 className="text-xl font-bold text-white mb-4">Daily Energy Consumption</h2>
            <ResponsiveContainer width="100%" height={300}>
              <BarChart data={chartData}>
                <CartesianGrid strokeDasharray="3 3" stroke="#ffffff20" />
                <XAxis dataKey="name" stroke="#fff" />
                <YAxis stroke="#fff" label={{ value: 'kWh', angle: -90, position: 'insideLeft', fill: '#fff' }} />
                <Tooltip contentStyle={{ backgroundColor: '#1e293b', border: '1px solid #475569' }} />
                <Bar dataKey="dailyKwh" fill="#3b82f6" />
              </BarChart>
            </ResponsiveContainer>
          </div>

          {/* Pie Chart */}
          <div className="bg-white/10 backdrop-blur-lg rounded-xl p-6 border border-white/20">
            <h2 className="text-xl font-bold text-white mb-4">Energy Distribution</h2>
            <ResponsiveContainer width="100%" height={300}>
              <PieChart>
                <Pie
                  data={chartData}
                  dataKey="dailyKwh"
                  nameKey="name"
                  cx="50%"
                  cy="50%"
                  outerRadius={100}
                  label={(entry) => ${entry.name}: ${entry.dailyKwh.toFixed(1)} kWh}
                >
                  {chartData.map((entry, index) => (
                    <Cell key={cell-${index}} fill={COLORS[index % COLORS.length]} />
                  ))}
                </Pie>
                <Tooltip contentStyle={{ backgroundColor: '#1e293b', border: '1px solid #475569' }} />
              </PieChart>
            </ResponsiveContainer>
          </div>
        </div>
      </div>
    </div>
  );
};

export default EnergyDashboard;
