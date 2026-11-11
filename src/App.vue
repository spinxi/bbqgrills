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
import CartDrawer from './components/CartDrawer.vue'
import DeliverySection from './components/DeliverySection.vue'
import WhyUsSection from './components/WhyUsSection.vue'
import FooterSection from './components/FooterSection.vue'
import MobileBottomNav from './components/MobileBottomNav.vue'

import { 
  Search, 
  RotateCcw,
  Flame,
  MessageCircle
} from 'lucide-vue-next'

// Reactive Language State
const currentLang = ref('ka')
const t = computed(() => translations[currentLang.value] || translations.ka)

// Search & Filter State
const searchQuery = ref('')
const selectedCategory = ref('all')
const sortBy = ref('featured')
const onlyBlackFriday = ref(false)

// Shopping Cart State
const flagshipProduct = productsData.find(p => p.id === 'model-5-xl-black-friday') || productsData[0]
const cartItems = ref([
  {
    product: flagshipProduct,
    quantity: 1
  }
])

// Modals State
const isCartOpen = ref(false)
const isSearchModalOpen = ref(false)
const isQuickViewModalOpen = ref(false)
const selectedProductForQuickView = ref(null)

// Cart calculations
const cartCount = computed(() => {
  return cartItems.value.reduce((acc, item) => acc + item.quantity, 0)
})

// Count by category for the Bento Grid
const productsCountByCategory = computed(() => {
  const counts = {
    all: productsData.length,
    'commercial': 0,
    'charcoal-ceramic': 0,
    'accessories': 0
  }
  productsData.forEach(p => {
    if (counts[p.category] !== undefined) {
      counts[p.category]++
    }
  })
  return counts
})

// Category tabs
const categoriesList = computed(() => [
  { id: 'all', label: t.value.catAll },
  { id: 'commercial', label: t.value.catCommercial },
  { id: 'charcoal-ceramic', label: t.value.catCharcoalCeramic },
  { id: 'accessories', label: t.value.catAccessories }
])

// Filtered and Sorted Products
const filteredProducts = computed(() => {
  let list = productsData.filter(p => {
    // Category Filter
    if (selectedCategory.value !== 'all' && p.category !== selectedCategory.value) {
      return false
    }

    // Black Friday Filter
    if (onlyBlackFriday.value && !p.isBlackFriday) {
      return false
    }

    // Search Query (Multilingual)
    if (searchQuery.value.trim()) {
      const q = searchQuery.value.toLowerCase().trim()
      const titleKa = (p.title?.ka || '').toLowerCase()
      const titleEn = (p.title?.en || '').toLowerCase()
      const titleRu = (p.title?.ru || '').toLowerCase()
      const sku = (p.sku || '').toLowerCase()
      const specs = Object.values(p.specs || {}).join(' ').toLowerCase()
      
      const match = titleKa.includes(q) || 
                    titleEn.includes(q) || 
                    titleRu.includes(q) || 
                    sku.includes(q) || 
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
    // Black Friday & Bestsellers first
    list.sort((a, b) => {
      if (a.isBlackFriday && !b.isBlackFriday) return -1
      if (!a.isBlackFriday && b.isBlackFriday) return 1
      if (a.isBestseller && !b.isBestseller) return -1
      if (!a.isBestseller && b.isBestseller) return 1
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

const handleSelectCategoryFromBento = (catId) => {
  selectedCategory.value = catId
  scrollSection('catalog')
}

const handleSelectProductFromSearch = (product) => {
  openQuickView(product)
}

const resetAllFilters = () => {
  selectedCategory.value = 'all'
  sortBy.value = 'featured'
  searchQuery.value = ''
  onlyBlackFriday.value = false
}

const scrollSection = (id) => {
  const el = document.getElementById(id)
  if (el) {
    el.scrollIntoView({ behavior: 'smooth' })
  }
}
</script>

<template>
  <div class="min-h-screen bg-zinc-950 text-zinc-100 flex flex-col font-sans selection:bg-orange-600 selection:text-white pt-16 md:pt-20 pb-20 md:pb-0">
    
    <!-- 1. Header with Brand, Search & Cart (No Overflow!) -->
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

    <!-- 2. Hero Section Featuring Black Friday Deal -->
    <HeroSection
      :t="t"
      @scroll-to-grid="scrollSection('catalog')"
      @scroll-to-delivery="scrollSection('delivery-section')"
      @add-flagship-to-cart="addToCart(flagshipProduct)"
    />

    <!-- 3. Category Bento Grid Section -->
    <CategoryBentoSection
      :selected-category="selectedCategory"
      :products-count-by-category="productsCountByCategory"
      :t="t"
      @select-category="handleSelectCategoryFromBento"
    />

    <!-- 4. Product Catalog Section with Unified Filter Toolbar -->
    <main id="catalog" class="py-8 sm:py-16 max-w-7xl mx-auto px-4 sm:px-6 w-full flex-1">
      
      <!-- Unified Filter Toolbar -->
      <div class="mb-6 bg-zinc-900/90 border border-zinc-800 rounded-2xl p-2.5 sm:p-3 flex flex-col lg:flex-row items-stretch lg:items-center justify-between gap-3 sm:gap-4 shadow-xl">
        
        <!-- Left side (Category Segmented Bar) -->
        <div class="flex items-center gap-2 overflow-x-auto max-w-full pb-1 lg:pb-0 scrollbar-none">
          <span class="text-xs font-bold text-zinc-400 uppercase tracking-wider shrink-0 pl-1">
            {{ t.filterCategoryLabel }}
          </span>
          <div class="flex items-center bg-zinc-950 p-1 rounded-xl border border-zinc-800/80 gap-1 overflow-x-auto scrollbar-none">
            <button
              v-for="cat in categoriesList"
              :key="cat.id"
              @click="selectedCategory = cat.id"
              class="whitespace-nowrap px-3 py-1.5 rounded-lg text-xs font-medium transition-all duration-200 cursor-pointer"
              :class="selectedCategory === cat.id
                ? 'bg-orange-600 text-white font-bold shadow-md shadow-orange-950/40'
                : 'text-zinc-400 hover:text-white hover:bg-zinc-900/60'"
            >
              {{ cat.label }}
            </button>
          </div>
        </div>

        <!-- Right side (Controls: Black Friday, Sort) -->
        <div class="flex items-center gap-2 sm:gap-3 w-full lg:w-auto justify-between lg:justify-end">
          
          <!-- Black Friday Filter Toggle -->
          <button
            @click="onlyBlackFriday = !onlyBlackFriday"
            class="flex items-center gap-1.5 px-3 py-1.5 rounded-xl text-xs font-bold transition-all cursor-pointer border"
            :class="onlyBlackFriday 
              ? 'bg-orange-600 text-white border-orange-500 shadow-md shadow-orange-950/40' 
              : 'bg-zinc-950 text-zinc-400 hover:text-white border-zinc-800'"
          >
            <Flame class="w-3.5 h-3.5" :class="onlyBlackFriday ? 'fill-white' : 'text-orange-500'" />
            <span class="whitespace-nowrap">{{ t.blackFridayFilter }}</span>
          </button>

          <!-- Sort Dropdown -->
          <div class="flex items-center justify-between gap-1.5 bg-zinc-950 border border-zinc-800 rounded-xl px-3 py-1.5 text-xs">
            <span class="text-zinc-400 font-medium whitespace-nowrap">{{ t.sortPrefix }}</span>
            <select
              v-model="sortBy"
              class="bg-transparent text-white outline-none cursor-pointer text-xs font-semibold pr-1 text-right"
            >
              <option value="featured" class="bg-zinc-900 text-white">{{ t.sortPopular }}</option>
              <option value="price-asc" class="bg-zinc-900 text-white">{{ t.sortPriceAsc }}</option>
              <option value="price-desc" class="bg-zinc-900 text-white">{{ t.sortPriceDesc }}</option>
            </select>
          </div>

        </div>

      </div>

      <!-- Filter Count & Reset Row -->
      <div class="flex items-center justify-between text-xs text-zinc-500 mb-6 sm:mb-8">
        <div>
          {{ t.productsFound }} <strong class="text-zinc-300 font-mono">{{ filteredProducts.length }}</strong>
        </div>

        <button
          v-if="selectedCategory !== 'all' || sortBy !== 'featured' || searchQuery || onlyBlackFriday"
          @click="resetAllFilters"
          class="text-zinc-400 hover:text-white flex items-center gap-1.5 transition-colors cursor-pointer"
        >
          <RotateCcw class="w-3.5 h-3.5" />
          <span>{{ t.clearFilters }}</span>
        </button>
      </div>

      <!-- Product Cards Grid (Responsive 1/2/4 cols) -->
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
          class="px-5 py-2 rounded-full bg-orange-600 hover:bg-orange-500 text-white text-xs font-semibold cursor-pointer transition-colors"
        >
          {{ t.resetFiltersBtn }}
        </button>
      </div>

    </main>

    <!-- 5. Delivery & Payment Section -->
    <DeliverySection
      :t="t"
      :current-lang="currentLang"
    />

    <!-- 6. Why Choose BBQ Grills Georgia Section -->
    <WhyUsSection
      :t="t"
    />

    <!-- 7. Footer -->
    <FooterSection
      :t="t"
    />

    <!-- Live Search Modal -->
    <SearchModal
      :is-open="isSearchModalOpen"
      :products="productsData"
      :current-lang="currentLang"
      :t="t"
      @close="isSearchModalOpen = false"
      @select-product="handleSelectProductFromSearch"
    />

    <!-- Quick View Modal -->
    <QuickViewModal
      :is-open="isQuickViewModalOpen"
      :product="selectedProductForQuickView"
      :current-lang="currentLang"
      :t="t"
      @close="isQuickViewModalOpen = false"
      @add-to-cart="addToCart"
    />

    <!-- Cart Drawer with Direct Website Checkout -->
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

    <!-- Mobile Bottom Navigation Bar -->
    <MobileBottomNav
      :cart-count="cartCount"
      :t="t"
      @open-cart="isCartOpen = true"
      @open-search="isSearchModalOpen = true"
      @scroll-to-catalog="scrollSection('catalog')"
      @scroll-to-delivery="scrollSection('delivery-section')"
    />

    <!-- Floating WhatsApp Customer Communication Button -->
    <aside aria-label="Customer Support" class="fixed bottom-20 md:bottom-6 right-3 md:right-6 z-40">
      <a
        href="https://wa.me/995511195755"
        target="_blank"
        rel="noopener noreferrer"
        class="flex items-center justify-center gap-2.5 w-11 h-11 md:w-auto md:h-auto md:px-4 md:py-3 rounded-full bg-emerald-600 hover:bg-emerald-500 text-white font-bold text-xs sm:text-sm shadow-2xl shadow-emerald-950/60 transition-all duration-300 hover:scale-105 cursor-pointer border border-emerald-400/40 group backdrop-blur-sm"
        :title="t.whatsappChat"
      >
        <span class="relative flex h-2 w-2 md:h-2.5 md:w-2.5 shrink-0">
          <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-300 opacity-75"></span>
          <span class="relative inline-flex rounded-full h-2 w-2 md:h-2.5 md:w-2.5 bg-emerald-400"></span>
        </span>
        <MessageCircle class="w-5 h-5 fill-white/20 text-white shrink-0" />
        <span class="hidden md:inline font-semibold">{{ t.whatsappChat }}</span>
      </a>
    </aside>

  </div>
</template>
