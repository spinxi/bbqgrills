<script setup>
import { ref, computed } from 'vue'
import { productsData } from './data/products'
import { translations } from './data/translations'

import HeaderNav from './components/HeaderNav.vue'
import HeroSection from './components/HeroSection.vue'
import CategoryBentoSection from './components/CategoryBentoSection.vue'
import ProductCard from './components/ProductCard.vue'
import QuickViewModal from './components/QuickViewModal.vue'
import SearchModal from './components/SearchModal.vue'
import SmsNotifyModal from './components/SmsNotifyModal.vue'
import CartDrawer from './components/CartDrawer.vue'
import ShowroomsSection from './components/ShowroomsSection.vue'
import WhyUsSection from './components/WhyUsSection.vue'
import FooterSection from './components/FooterSection.vue'
import MobileBottomNav from './components/MobileBottomNav.vue'

import { 
  Search, 
  RotateCcw,
  Bell
} from 'lucide-vue-next'

// Reactive Language State
const currentLang = ref('ka')
const t = computed(() => translations[currentLang.value] || translations.ka)

// Search & Filter State
const searchQuery = ref('')
const selectedCategory = ref('all')
const selectedBrand = ref('all')
const selectedBranch = ref('all')
const sortBy = ref('featured')
const hideOutOfStock = ref(false)

// Shopping Cart State
const cartItems = ref([
  {
    product: productsData.find(p => p.id === 'ooni-karu-16'),
    quantity: 1
  }
])

// Modals State
const isCartOpen = ref(false)
const isSearchModalOpen = ref(false)
const isQuickViewModalOpen = ref(false)
const selectedProductForQuickView = ref(null)
const isSmsModalOpen = ref(false)
const selectedProductForSms = ref(null)

// SMS notification toast
const showSmsToast = ref(false)
const smsToastMessage = ref('')

// Cart calculations
const cartCount = computed(() => {
  return cartItems.value.reduce((acc, item) => acc + item.quantity, 0)
})

// Count by category for the Bento Grid
const productsCountByCategory = computed(() => {
  const counts = {
    all: productsData.length,
    'charcoal-ceramic': 0,
    gas: 0,
    pizza: 0,
    cutlery: 0,
    accessories: 0
  }
  productsData.forEach(p => {
    if (counts[p.category] !== undefined) {
      counts[p.category]++
    }
  })
  return counts
})

// Brand list for Brand Segmented Bar
const brandsList = computed(() => [
  { id: 'all', label: t.value.brandAll },
  { id: 'Weber', label: 'Weber' },
  { id: 'Big Green Egg', label: 'Big Green Egg' },
  { id: 'Ooni', label: 'Ooni' },
  { id: 'Lodge', label: 'Lodge' },
  { id: 'Joseph Joseph', label: 'Joseph Joseph' }
])

// Filtered and Sorted Products
const filteredProducts = computed(() => {
  let list = productsData.filter(p => {
    // Brand Filter
    if (selectedBrand.value !== 'all' && p.brand !== selectedBrand.value) {
      return false
    }

    // Category Filter
    if (selectedCategory.value !== 'all' && p.category !== selectedCategory.value) {
      return false
    }

    // Branch Filter
    if (selectedBranch.value === 'beliashvili' && (p.stockBeliashvili || 0) <= 0) {
      return false
    }
    if (selectedBranch.value === 'citymall' && (p.stockCityMall || 0) <= 0) {
      return false
    }

    // Smart Out-of-Stock Filter
    if (hideOutOfStock.value) {
      const totalStock = (p.stockBeliashvili || 0) + (p.stockCityMall || 0)
      if (totalStock <= 0) return false
    }

    // Search Query (Multilingual)
    if (searchQuery.value.trim()) {
      const q = searchQuery.value.toLowerCase().trim()
      const titleKa = (p.title?.ka || '').toLowerCase()
      const titleEn = (p.title?.en || '').toLowerCase()
      const titleRu = (p.title?.ru || '').toLowerCase()
      const brand = (p.brand || '').toLowerCase()
      const specs = Object.values(p.specs || {}).join(' ').toLowerCase()
      
      const match = titleKa.includes(q) || 
                    titleEn.includes(q) || 
                    titleRu.includes(q) || 
                    brand.includes(q) || 
                    specs.includes(q)
      if (!match) return false
    }

    return true
  })

  // Sorting logic
  if (sortBy.value === 'price-asc') {
    list.sort((a, b) => a.price - b.price)
  } else if (sortBy.value === 'price-desc') {
    list.sort((a, b) => b.price - a.price)
  } else {
    // Stock-First Priority Sorting (Popular / Featured)
    list.sort((a, b) => {
      const stockA = (a.stockBeliashvili || 0) + (a.stockCityMall || 0)
      const stockB = (b.stockBeliashvili || 0) + (b.stockCityMall || 0)
      if (stockA > 0 && stockB <= 0) return -1
      if (stockA <= 0 && stockB > 0) return 1
      return 0
    })
  }

  return list
})

// Handlers
const openQuickView = (product) => {
  selectedProductForQuickView.value = product
  isQuickViewModalOpen.value = true
}

const openSmsModal = (product) => {
  selectedProductForSms.value = product
  isSmsModalOpen.value = true
}

const addToCart = (product, qty = 1) => {
  const existing = cartItems.value.find(item => item.product.id === product.id)
  if (existing) {
    existing.quantity += qty
  } else {
    cartItems.value.push({ product, quantity: qty })
  }
  isCartOpen.value = true
}

const updateCartQuantity = (productId, newQty) => {
  if (newQty <= 0) {
    removeFromCart(productId)
  } else {
    const item = cartItems.value.find(i => i.product.id === productId)
    if (item) item.quantity = newQty
  }
}

const removeFromCart = (productId) => {
  cartItems.value = cartItems.value.filter(i => i.product.id !== productId)
}

const handleSmsSubscribed = ({ phone, product }) => {
  smsToastMessage.value = `${phone} წარმატებით დარეგისტრირდა ${product.brand}-ის მარაგის შევსებაზე.`
  showSmsToast.value = true
  setTimeout(() => {
    showSmsToast.value = false
  }, 4000)
}

const handleSelectCategoryFromBento = (catId) => {
  selectedCategory.value = catId
  scrollSection('catalog')
}

const handleSelectProductFromSearch = (product) => {
  openQuickView(product)
}

const resetAllFilters = () => {
  selectedBrand.value = 'all'
  selectedCategory.value = 'all'
  selectedBranch.value = 'all'
  sortBy.value = 'featured'
  searchQuery.value = ''
  hideOutOfStock.value = false
}

const scrollSection = (id) => {
  const el = document.getElementById(id)
  if (el) {
    el.scrollIntoView({ behavior: 'smooth' })
  }
}
</script>

<template>
  <div class="min-h-screen bg-zinc-950 text-zinc-100 flex flex-col font-sans selection:bg-red-600 selection:text-white pt-20 pb-20 md:pb-0">
    
    <!-- 1. Clean, Single-Row Luxury Header with Wide Search Trigger (h-20) -->
    <HeaderNav
      :current-lang="currentLang"
      :t="t"
      :cart-count="cartCount"
      @update:current-lang="currentLang = $event"
      @open-cart="isCartOpen = true"
      @open-search="isSearchModalOpen = true"
      @filter-category="selectedCategory = $event; scrollSection('catalog')"
      @scroll-to-section="scrollSection($event)"
    />

    <!-- 2. Spacious Visual Luxury Hero Section -->
    <HeroSection
      :t="t"
      @scroll-to-grid="scrollSection('catalog')"
      @scroll-to-showrooms="scrollSection('showrooms')"
    />

    <!-- 3. Dedicated Visual "Shop by Category" Bento Grid Section -->
    <CategoryBentoSection
      :selected-category="selectedCategory"
      :products-count-by-category="productsCountByCategory"
      :t="t"
      @select-category="handleSelectCategoryFromBento"
    />

    <!-- 4. Product Catalog Section with Unified Filter Toolbar -->
    <main id="catalog" class="py-8 sm:py-16 max-w-7xl mx-auto px-4 sm:px-6 w-full flex-1">
      
      <!-- Unified Filter Toolbar (Responsive Flex / Grid) -->
      <div class="mb-6 bg-zinc-900/90 border border-zinc-800 rounded-2xl p-2.5 sm:p-3 flex flex-col lg:flex-row items-stretch lg:items-center justify-between gap-3 sm:gap-4 shadow-xl">
        
        <!-- Left side (Brand Segmented Bar) -->
        <div class="flex items-center gap-2 overflow-x-auto max-w-full pb-1 lg:pb-0 scrollbar-none">
          <span class="text-xs font-bold text-zinc-400 uppercase tracking-wider shrink-0 pl-1">
            {{ t.brandLabel }}
          </span>
          <div class="flex items-center bg-zinc-950 p-1 rounded-xl border border-zinc-800/80 gap-1 overflow-x-auto scrollbar-none">
            <button
              v-for="brand in brandsList"
              :key="brand.id"
              @click="selectedBrand = brand.id"
              class="whitespace-nowrap px-2.5 sm:px-3 py-1.5 rounded-lg text-xs font-medium transition-all duration-200 cursor-pointer"
              :class="selectedBrand === brand.id
                ? 'bg-red-600 text-white font-semibold shadow shadow-red-950/40'
                : 'text-zinc-400 hover:text-white hover:bg-zinc-900/60'"
            >
              {{ brand.label }}
            </button>
          </div>
        </div>

        <!-- Right side (Controls: Branch, Sort, In-Stock) -->
        <div class="grid grid-cols-2 sm:flex items-center gap-2 sm:gap-3 w-full lg:w-auto">
          
          <!-- 1. Branch Filter Dropdown -->
          <div class="col-span-1 flex items-center justify-between gap-1.5 bg-zinc-950 border border-zinc-800 rounded-xl px-2.5 sm:px-3 py-1.5 text-xs">
            <span class="text-zinc-400 font-medium whitespace-nowrap">{{ t.branchPrefix }}</span>
            <select
              v-model="selectedBranch"
              class="bg-transparent text-white outline-none cursor-pointer text-xs font-semibold pr-1 w-full text-right"
            >
              <option value="all" class="bg-zinc-900 text-white">{{ t.branchAll }}</option>
              <option value="beliashvili" class="bg-zinc-900 text-white">{{ t.branchBeliashviliShort }}</option>
              <option value="citymall" class="bg-zinc-900 text-white">{{ t.branchCityMallShort }}</option>
            </select>
          </div>

          <!-- 2. Sort Dropdown -->
          <div class="col-span-1 flex items-center justify-between gap-1.5 bg-zinc-950 border border-zinc-800 rounded-xl px-2.5 sm:px-3 py-1.5 text-xs">
            <span class="text-zinc-400 font-medium whitespace-nowrap">{{ t.sortPrefix }}</span>
            <select
              v-model="sortBy"
              class="bg-transparent text-white outline-none cursor-pointer text-xs font-semibold pr-1 w-full text-right"
            >
              <option value="featured" class="bg-zinc-900 text-white">{{ t.sortPopular }}</option>
              <option value="price-asc" class="bg-zinc-900 text-white">{{ t.sortPriceAsc }}</option>
              <option value="price-desc" class="bg-zinc-900 text-white">{{ t.sortPriceDesc }}</option>
            </select>
          </div>

          <!-- 3. Clean Toggle Switch: მხოლოდ მარაგში -->
          <label class="col-span-2 sm:col-span-1 flex items-center justify-center sm:justify-start gap-2 bg-zinc-950 border border-zinc-800 rounded-xl px-3 py-1.5 text-xs cursor-pointer select-none">
            <input
              type="checkbox"
              v-model="hideOutOfStock"
              class="sr-only peer"
            />
            <div class="w-8 h-4 bg-zinc-800 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:rounded-full after:h-3 after:w-3 after:transition-all peer-checked:bg-red-600 relative"></div>
            <span class="text-zinc-300 font-medium peer-checked:text-white whitespace-nowrap">
              {{ t.inStockOnlyShort }}
            </span>
          </label>

        </div>

      </div>

      <!-- Filter Count & Reset Row -->
      <div class="flex items-center justify-between text-xs text-zinc-500 mb-6 sm:mb-8">
        <div>
          {{ t.productsFound }} <strong class="text-zinc-300 font-mono">{{ filteredProducts.length }}</strong>
        </div>

        <button
          v-if="selectedBrand !== 'all' || selectedCategory !== 'all' || selectedBranch !== 'all' || sortBy !== 'featured' || searchQuery || hideOutOfStock"
          @click="resetAllFilters"
          class="text-zinc-400 hover:text-white flex items-center gap-1.5 transition-colors cursor-pointer"
        >
          <RotateCcw class="w-3.5 h-3.5" />
          <span>{{ t.clearFilters }}</span>
        </button>
      </div>

      <!-- 4-Column Editorial Product Cards Grid (Responsive 1/2/4 cols) -->
      <div 
        v-if="filteredProducts.length > 0"
        class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 sm:gap-6 lg:gap-8"
      >
        <ProductCard
          v-for="product in filteredProducts"
          :key="product.id"
          :product="product"
          :current-lang="currentLang"
          :t="t"
          :is-in-cart="cartItems.some(i => i.product.id === product.id)"
          @add-to-cart="addToCart"
          @open-sms="openSmsModal"
          @quick-view="openQuickView"
        />
      </div>

      <!-- Empty State -->
      <div v-else class="text-center py-24 space-y-4">
        <div class="w-12 h-12 rounded-full bg-zinc-900 text-zinc-600 flex items-center justify-center mx-auto">
          <Search class="w-6 h-6" />
        </div>
        <h3 class="text-lg font-semibold text-white">{{ t.noProductsTitle }}</h3>
        <p class="text-xs text-zinc-400 max-w-sm mx-auto">
          {{ t.noProductsDesc }}
        </p>
        <button
          @click="resetAllFilters"
          class="px-5 py-2 rounded-full bg-zinc-800 hover:bg-zinc-700 text-white text-xs font-semibold cursor-pointer transition-colors"
        >
          {{ t.resetFiltersBtn }}
        </button>
      </div>

    </main>

    <!-- 5. Showrooms Section with Multi-Branch Inventory -->
    <ShowroomsSection
      :t="t"
      :current-lang="currentLang"
      @filter-branch="selectedBranch = $event; scrollSection('catalog')"
    />

    <!-- 6. Why Choose Grills.ge Section -->
    <WhyUsSection
      :t="t"
    />

    <!-- 7. Clean Luxury Footer -->
    <FooterSection
      :t="t"
    />

    <!-- Authentic Retail Modals -->

    <!-- Interactive Live Search Modal (Spotlight Style) -->
    <SearchModal
      :is-open="isSearchModalOpen"
      :products="productsData"
      :current-lang="currentLang"
      :t="t"
      @close="isSearchModalOpen = false"
      @select-product="handleSelectProductFromSearch"
    />

    <!-- Product Quick View Modal with 3-Thumbnails Gallery, Specs Table & Quantity Selector -->
    <QuickViewModal
      :is-open="isQuickViewModalOpen"
      :product="selectedProductForQuickView"
      :current-lang="currentLang"
      :t="t"
      @close="isQuickViewModalOpen = false"
      @add-to-cart="addToCart"
      @open-sms="openSmsModal"
    />

    <!-- SMS Restock Lead Capture Modal for 0-stock products -->
    <SmsNotifyModal
      :is-open="isSmsModalOpen"
      :product="selectedProductForSms"
      :current-lang="currentLang"
      :t="t"
      @close="isSmsModalOpen = false"
      @sms-subscribed="handleSmsSubscribed"
    />

    <!-- Shopping Cart Drawer with Clean Payment Badges -->
    <CartDrawer
      :is-open="isCartOpen"
      :items="cartItems"
      :current-lang="currentLang"
      :t="t"
      @close="isCartOpen = false"
      @update-quantity="updateCartQuantity"
      @remove-item="removeFromCart"
      @clear-cart="cartItems = []"
    />

    <!-- SMS Toast Confirmation -->
    <div 
      v-if="showSmsToast"
      class="fixed bottom-6 left-6 z-50 max-w-sm p-4 rounded-2xl bg-zinc-900 border border-zinc-700 shadow-2xl text-white backdrop-blur-md animate-in slide-in-from-bottom-5 duration-300"
    >
      <div class="flex items-center gap-3">
        <Bell class="w-4 h-4 text-red-500 shrink-0" />
        <div class="text-xs text-zinc-300">
          {{ smsToastMessage }}
        </div>
      </div>
    </div>

    <!-- Mobile Bottom Navigation Dock (Thumb-friendly for smartphone users) -->
    <MobileBottomNav
      :cart-count="cartCount"
      :t="t"
      @open-cart="isCartOpen = true"
      @open-search="isSearchModalOpen = true"
      @scroll-to-catalog="scrollSection('catalog')"
      @scroll-to-showrooms="scrollSection('showrooms')"
    />

  </div>
</template>
