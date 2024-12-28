// Import dependencies
import React, { useState, useEffect } from 'react';
import {
  DataGrid,
  GridToolbarContainer,
  GridToolbarFilterButton,
} from '@mui/x-data-grid';
import { BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, Legend } from 'recharts';
import { Button, Modal, TextField, Box } from '@mui/material';

// Sample Data (replace with your data source)
const sampleData = [
  { id: 1, company: "A Corp", status: "Out of Service", date: "2024-01-15" },
  { id: 2, company: "B LLC", status: "Active", date: "2024-02-10" },
  { id: 3, company: "C Inc", status: "Out of Service", date: "2024-02-20" },
];

const App = () => {
  // State Management
  const [tableData, setTableData] = useState(sampleData);
  const [filteredData, setFilteredData] = useState(sampleData);
  const [chartData, setChartData] = useState([]);

  // Update chart data based on filtered data
  useEffect(() => {
    const grouped = filteredData.reduce((acc, curr) => {
      if (curr.status === "Out of Service") {
        const month = new Date(curr.date).toLocaleString('default', { month: 'short' });
        acc[month] = (acc[month] || 0) + 1;
      }
      return acc;
    }, {});

    const chart = Object.keys(grouped).map(month => ({ month, count: grouped[month] }));
    setChartData(chart);
  }, [filteredData]);

  // Column Configuration
  const columns = [
    { field: 'company', headerName: 'Company', flex: 1 },
    { field: 'status', headerName: 'Status', flex: 1 },
    {
      field: 'date',
      headerName: 'Date',
      flex: 1,
      editable: true,
      type: 'date',
    },
  ];

  // Filter Handler
  const handleFilter = (searchTerm) => {
    const filtered = tableData.filter(row =>
      row.company.toLowerCase().includes(searchTerm.toLowerCase())
    );
    setFilteredData(filtered);
  };

  return (
    <div style={{ padding: '20px' }}>
      <h1>React Material-UI App</h1>

      {/* Search Bar */}
      <TextField
        label="Search Company"
        variant="outlined"
        fullWidth
        style={{ marginBottom: '20px' }}
        onChange={(e) => handleFilter(e.target.value)}
      />

      {/* DataGrid Table */}
      <div style={{ height: 400, marginBottom: '20px' }}>
        <DataGrid
          rows={filteredData}
          columns={columns}
          pageSize={5}
          rowsPerPageOptions={[5, 10]}
          onCellEditCommit={(params) => {
            const updated = [...tableData];
            const index = updated.findIndex(row => row.id === params.id);
            updated[index][params.field] = params.value;
            setTableData(updated);
            setFilteredData(updated);
          }}
          components={{
            Toolbar: () => (
              <GridToolbarContainer>
                <GridToolbarFilterButton />
              </GridToolbarContainer>
            ),
          }}
        />
      </div>

      {/* Bar Chart */}
      <div style={{ height: 300 }}>
        <BarChart
          width={600}
          height={300}
          data={chartData}
          margin={{ top: 5, right: 30, left: 20, bottom: 5 }}
        >
          <CartesianGrid strokeDasharray="3 3" />
          <XAxis dataKey="month" />
          <YAxis />
          <Tooltip />
          <Legend />
          <Bar dataKey="count" fill="#8884d8" />
        </BarChart>
      </div>
    </div>
  );
};

export default App;
 create-react-app
Key Features 1. Data Table with Live Chart Updates  Data Visualization: Bar charts dynamically update based on the table's filtered data. Chart shows the count of companies "Out of Service" per month. Customizable Table: 
