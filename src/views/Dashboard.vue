<template>
  <div class="dashboard">
    <h1>Threat Intelligence Dashboard</h1>

    <TimeFilter @change="changeTimeRange" />

    

    <!-- SUMMARY CARDS -->
    <div class="cards">
      <SummaryCard title="Total Threats" :value="summary.totalThreats" />
      <SummaryCard title="Today Threats" :value="summary.todayThreats" />
      <SummaryCard title="Top Threat" :value="summary.topThreatType" />
      <SummaryCard title="Top Country" :value="summary.topCountry" />
    </div>

    <!-- CHARTS -->
    <div class="charts">
      <div class="chart">
        <ThreatChart />
      </div>

      <div class="chart">
        <PlatformChart />
      </div>

      <div class="chart">
        <CountryChart />
      </div>

      <div class="chart">
        <TopThreatRulesChart />
      </div>
    </div>

    <!-- WORLD MAP -->
    <div class="chart full-width">
      <ThreatWorldMap />
    </div>

    <!-- TABLE -->
    <div class="table">
      <RecentThreatTable />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import api from "../api/axios";

import SummaryCard from "../components/SummaryCard.vue";
import ThreatChart from "../components/ThreatChart.vue";
import PlatformChart from "../components/PlatformChart.vue";
import CountryChart from "../components/CountryChart.vue";
import RecentThreatTable from "../components/RecentThreatTable.vue";
import TopThreatRulesChart from "../components/TopThreatRulesChart.vue";
import ThreatWorldMap from "../components/ThreatWorldMap.vue";
import TimeFilter from "../components/TimeFilter.vue";

const summary = ref({
  totalThreats: 0,
  todayThreats: 0,
  topThreatType: "-",
  topCountry: "-"
});

const days = ref(7);

const loadDashboard = async () => {
  try {
    const { data } = await api.get(`/api/dashboard/summary?days=${days.value}`);

    summary.value = {
      totalThreats: Number(data.totalThreats ?? 0),
      todayThreats: Number(data.todayThreats ?? 0),
      topThreatType: data.topThreatType ?? "-",
      topCountry: data.topCountry ?? "-"
    };
  } catch (err) {
    console.error("ERROR LOAD DASHBOARD:", err);
  }
};

const changeTimeRange = (value) => {
  days.value = value;
  loadDashboard();
};

let interval = null;

onMounted(() => {
  loadDashboard();
  // interval = setInterval(loadDashboard, 10000);
});

onUnmounted(() => {
  if (interval) clearInterval(interval);
});
</script>

<style>
/* =========================
   GLOBAL DASHBOARD
   ========================= */
.dashboard {
  padding: 30px;
  font-family: Arial;
  background: #f3f4f6;
}

/* =========================
   SUMMARY CARDS (FIX UTAMA)
   ========================= */
.cards {
  display: flex;   /* 🔥 GANTI DARI GRID */
  gap: 20px;
  margin-bottom: 30px;
}

.cards > * {
  flex: 1;
}

/* =========================
   CHARTS GRID
   ========================= */
.charts {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  margin-bottom: 30px;
}

/* =========================
   CHART BOX
   ========================= */
.chart {
  background: white;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);

  position: relative;
  z-index: 1;
}

/* FULL WIDTH CHART (MAP) */
.full-width {
  grid-column: span 2;
  margin-bottom: 30px;
}

/* =========================
   TABLE
   ========================= */
.table {
  background: white;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);

  position: relative;
  z-index: 1;
}
</style>