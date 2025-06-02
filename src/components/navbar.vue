<template>
  <transition name="sidebar">
    <aside :class="['sidebar-container', { collapsed } ]" role="navigation" aria-label="Sidebar menu">
      <div class="sidebar">
        <header class="sidebar-header">
          <button
            class="collapse-btn"
            @click="toggleCollapse"
            :aria-expanded="!collapsed"
            aria-label="Toggle sidebar"
          >
            <i :class="collapsed ? 'fas fa-chevron-right' : 'fas fa-chevron-left'"></i>
          </button>
          <h1 v-if="!collapsed" class="sidebar-brand">📚 Perpustakaan</h1>
        </header>

        <div v-if="!collapsed" class="sidebar-search" role="search">
          <label for="sidebar-search-input" class="sr-only">Cari menu</label>
          <input
            id="sidebar-search-input"
            type="search"
            v-model="search"
            placeholder="Cari menu..."
            class="search-input"
          />
          <i class="fas fa-search search-icon" aria-hidden="true"></i>
        </div>

        <ul class="sidebar-nav">
          <li v-for="item in filteredItems" :key="item.text">
            <router-link
              :to="item.to"
              class="nav-item"
              active-class="active"
              exact
              :aria-current="isActive(item.to) ? 'page' : null"
              :title="collapsed ? item.text : null"
            >
              <i :class="item.icon" class="nav-icon"></i>
              <span v-if="!collapsed" class="nav-text">{{ item.text }}</span>
            </router-link>
          </li>
        </ul>

        <footer class="sidebar-footer">
          <button
            class="logout-btn"
            @click="logout"
            aria-label="Logout"
            :title="collapsed ? 'Logout' : null"
          >
            <i class="fas fa-sign-out-alt"></i>
            <span v-if="!collapsed">Logout</span>
          </button>
        </footer>
      </div>
    </aside>
  </transition>
</template>

<script>
export default {
  name: 'Sidebar',
  data() {
    return {
      collapsed: false,
      search: '',
      items: [
        { to: '/dashboard', icon: 'fas fa-tachometer-alt', text: 'Dashboard' },
        { to: '/members', icon: 'fas fa-users', text: 'Member' },
        { to: '/buku', icon: 'fas fa-book', text: 'Kelola Buku' },
        { to: '/peminjaman', icon: 'fas fa-hand-holding', text: 'Peminjaman' },
        { to: '/pengembalian', icon: 'fas fa-undo-alt', text: 'Pengembalian' },
      ],
    };
  },
  computed: {
    filteredItems() {
      if (!this.search) return this.items;
      return this.items.filter(i =>
        i.text.toLowerCase().includes(this.search.toLowerCase())
      );
    },
  },
  methods: {
    toggleCollapse() {
      this.collapsed = !this.collapsed;
    },
    isActive(route) {
      return this.$route.path === route;
    },
    logout() {
      localStorage.removeItem('token');
      this.$router.push('/');
    },
  },
};
</script>

<style scoped>
:root {
  --sidebar-width: 260px;
  --sidebar-collapsed-width: 70px;
  --sidebar-bg: #1f1f1f;
  --sidebar-hover: #333;
  --accent-color: #00bcd4;
  --text-color: #eee;
  --transition-speed: 0.25s;
}

.sidebar-container {
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  width: var(--sidebar-width);
  background: var(--sidebar-bg);
  color: var(--text-color);
  transition: width var(--transition-speed) ease;
  box-shadow: 2px 0 8px rgba(0, 0, 0, 0.3);
  overflow: hidden;
  z-index: 100;
}

.sidebar.collapsed {
  width: var(--sidebar-collapsed-width);
}

.sidebar-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 20px;
  border-bottom: 1px solid #444;
}

.sidebar-brand {
  font-size: 1.5rem;
  font-weight: bold;
  color: var(--accent-color);
  transition: opacity var(--transition-speed);
}

.sidebar.collapsed .sidebar-brand {
  opacity: 0;
  pointer-events: none;
}

.collapse-btn {
  background: var(--accent-color);
  border: none;
  color: #fff;
  padding: 6px 12px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.9rem;
  transition: background var(--transition-speed);
}
.collapse-btn:hover {
  background: #008fa1;
}

.sidebar-search {
  padding: 16px;
}
.search-input {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #444;
  border-radius: 6px;
  background: #2a2a2a;
  color: var(--text-color);
}
.search-input:focus {
  outline: none;
  border-color: var(--accent-color);
}

.sidebar-nav {
  list-style: none;
  padding: 0;
  margin: 0;
  flex: 1;
}
.nav-item {
  display: flex;
  align-items: center;
  padding: 12px 20px;
  color: var(--text-color);
  text-decoration: none;
  transition: background var(--transition-speed);
  cursor: pointer;
}
.nav-item:hover {
  background: var(--sidebar-hover);
}
.nav-item.active {
  background: var(--accent-color);
  color: #fff;
}
.nav-icon {
  font-size: 1.2rem;
  margin-right: 14px;
  width: 24px;
  text-align: center;
}
.nav-text {
  transition: opacity var(--transition-speed);
}
.sidebar.collapsed .nav-text {
  opacity: 0;
  pointer-events: none;
}

.sidebar-footer {
  padding: 16px;
  border-top: 1px solid #444;
}
.logout-btn {
  width: 100%;
  background: transparent;
  border: 1px solid #f44336;
  color: #f44336;
  padding: 10px 16px;
  border-radius: 4px;
  cursor: pointer;
  display: flex;
  align-items: center;
  transition: background var(--transition-speed), color var(--transition-speed);
}
.logout-btn i {
  margin-right: 10px;
}
.logout-btn:hover {
  background: #f44336;
  color: #fff;
}

.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0,0,0,0);
  white-space: nowrap;
  border: 0;
}

@media (max-width: 768px) {
  :root {
    --sidebar-width: var(--sidebar-collapsed-width);
  }
  .sidebar-brand,
  .nav-text,
  .sidebar-search,
  .logout-btn span {
    display: none;
  }
}
</style>
