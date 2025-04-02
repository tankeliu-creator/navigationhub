<template>
  <div class="min-h-screen bg-slate-50">
    <nav class="bg-white shadow-sm border-b border-slate-200">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex justify-between h-16">
          <div class="flex">
            <div class="flex-shrink-0 flex items-center">
              <h1 class="text-xl font-medium text-slate-800">Navigation Hub</h1>
            </div>
          </div>
        </div>
      </div>
    </nav>

    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
      <div class="mb-12">
        <div class="max-w-2xl mx-auto">
          <div class="relative">
            <input
              type="text"
              v-model="searchQuery"
              placeholder="Search links..."
              class="w-full px-4 py-3 border border-slate-200 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent outline-none font-light bg-white"
              @input="filterLinks"
            />
          </div>
        </div>
      </div>

      <div v-for="(category, index) in filteredCategories" :key="index" class="mb-16">
        <h2 class="text-2xl font-semibold text-slate-800 mb-8 pb-2 border-b border-slate-200">{{ category.name }}</h2>
        <div v-for="(subcategory, subIndex) in category.subcategories" :key="subIndex" class="mb-12">
          <h3 class="text-lg font-medium text-slate-700 mb-6 ml-2 flex items-center">
            <span class="w-1.5 h-1.5 bg-blue-500 rounded-full mr-2"></span>
            {{ subcategory.name }}
          </h3>
          <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-5">
            <div
              v-for="link in subcategory.links"
              :key="link.id"
              class="bg-white rounded-xl shadow-sm hover:shadow-md transition-all duration-300 group relative border border-slate-100"
              @mouseenter="showTooltip(link.id, $event)"
              @mouseleave="hideTooltip"
              @mousemove="updateTooltipPosition($event)"
            >
              <a
                :href="link.url"
                target="_blank"
                rel="noopener noreferrer"
                class="block p-4 h-full"
              >
                <h3 class="text-base font-medium text-slate-800 group-hover:text-blue-600 transition-colors duration-300 mb-2">
                  {{ link.title }}
                </h3>
                <p class="text-sm text-slate-600 font-light truncate">
                  {{ link.description }}
                </p>
              </a>
            </div>
          </div>
        </div>
      </div>
    </main>

    <!-- Tooltip Portal -->
    <Transition
      enter-active-class="transition duration-200 ease-out"
      enter-from-class="opacity-0 translate-y-1"
      enter-to-class="opacity-100 translate-y-0"
      leave-active-class="transition duration-150 ease-in"
      leave-from-class="opacity-100 translate-y-0"
      leave-to-class="opacity-0 translate-y-1"
    >
      <div
        v-if="tooltipData.visible"
        class="fixed z-50 w-64 p-4 bg-slate-800 rounded-lg shadow-xl text-sm text-slate-100 pointer-events-none"
        :style="tooltipStyle"
      >
        {{ tooltipData.content }}
      </div>
    </Transition>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useWindowSize } from '@vueuse/core'

const searchQuery = ref('')
const { width: windowWidth, height: windowHeight } = useWindowSize()

const tooltipData = ref({
  visible: false,
  content: '',
  x: 0,
  y: 0
})

const tooltipStyle = computed(() => {
  const padding = 20
  const tooltipWidth = 256 // w-64
  const tooltipHeight = 100 // Approximate height

  let x = tooltipData.value.x + padding
  let y = tooltipData.value.y + padding

  // Prevent tooltip from going off the right edge
  if (x + tooltipWidth > windowWidth.value) {
    x = tooltipData.value.x - tooltipWidth - padding
  }

  // Prevent tooltip from going off the bottom edge
  if (y + tooltipHeight > windowHeight.value) {
    y = tooltipData.value.y - tooltipHeight - padding
  }

  return {
    top: `${y}px`,
    left: `${x}px`
  }
})

function showTooltip(id, event) {
  const link = findLinkById(id)
  if (link) {
    tooltipData.value = {
      visible: true,
      content: link.description,
      x: event.clientX,
      y: event.clientY
    }
  }
}

function hideTooltip() {
  tooltipData.value.visible = false
}

function updateTooltipPosition(event) {
  if (tooltipData.value.visible) {
    tooltipData.value.x = event.clientX
    tooltipData.value.y = event.clientY
  }
}

function findLinkById(id) {
  for (const category of categories.value) {
    for (const subcategory of category.subcategories) {
      const link = subcategory.links.find(link => link.id === id)
      if (link) return link
    }
  }
  return null
}

const categories = ref([
  {
    name: 'Development Tools',
    subcategories: [
      {
        name: 'Version Control',
        links: [
          {
            id: 1,
            title: 'GitHub',
            url: 'https://github.com',
            description: 'Web-based platform for version control and collaboration'
          },
          {
            id: 2,
            title: 'GitLab',
            url: 'https://gitlab.com',
            description: 'Complete DevOps platform with Git repository management'
          }
        ]
      },
      {
        name: 'Code Editors',
        links: [
          {
            id: 3,
            title: 'VS Code',
            url: 'https://code.visualstudio.com',
            description: 'Lightweight but powerful source code editor'
          },
          {
            id: 4,
            title: 'CodeSandbox',
            url: 'https://codesandbox.io',
            description: 'Online code editor for web development'
          }
        ]
      },
      {
        name: 'Demand gathering and keyword research',
        links: [
          {
            id: 34,
            title: 'SearchSuggest.Tips',
            url: 'https://www.searchsuggest.tips/',
            description: 'Provides search suggestion tools to help optimize search queries.'
          },
          {
            id: 35,
            title: 'Toolify Top',
            url: 'https://www.toolify.ai/Best-AI-Tools-revenue',
            description: 'AI-powered tool for helping users choose business optimization solutions.'
          },
          {
            id: 36,
            title: "There's An AI For That",
            url: 'https://theresanaiforthat.com/requests',
            description: 'Users can submit requirements and find or recommend AI tools for specific scenarios.'
          },
          {
            id: 37,
            title: 'Ahrefs Keyword Difficulty',
            url: 'https://ahrefs.com/keyword-difficulty',
            description: 'Evaluates SEO keyword competition and assists with content optimization.'
          }
        ]
      },
      {
        name: 'Domain name lookup',
        links: [
          {
            id: 38,
            title: 'Lean Domain Search',
            url: 'https://leandomainsearch.com/',
            description: 'Quickly generates and checks available domain names that are simple and easy to remember.'
          },
          {
            id: 39,
            title: 'Query Domains',
            url: 'https://query.domains/',
            description: 'Combines language analysis with domain name search to find creative and available domain names.'
          },
          {
            id: 40,
            title: 'Instant Domain Search',
            url: 'https://instantdomainsearch.com/',
            description: 'Real-time domain search tool that instantly shows domain registration status.'
          }
        ]
      },
      {
        name: 'Domain name registration',
        links: [
          {
            id: 41,
            title: 'Spaceship',
            url: 'https://spaceship.com',
            description: 'All-in-one domain registration and management platform with bulk operations support.'
          },
          {
            id: 42,
            title: 'Porkbun',
            url: 'https://porkbun.com',
            description: 'Premium domain registrar offering competitive pricing and excellent user experience.'
          },
          {
            id: 43,
            title: 'Namecheap',
            url: 'https://namecheap.com',
            description: 'Established domain name registrar known for affordable pricing and privacy protection.'
          }
        ]
      },
      {
        name: 'Code & Website Hosting',
        links: [
          {
            id: 44,
            title: 'GitHub',
            url: 'https://github.com',
            description: 'The world\'s leading code hosting platform, supporting collaborative development and open source projects.'
          },
          {
            id: 45,
            title: 'Cloudflare',
            url: 'https://cloudflare.com',
            description: 'Provides CDN, DNS, security protection and other web infrastructure services.'
          },
          {
            id: 46,
            title: 'Vercel',
            url: 'https://vercel.com',
            description: 'Platform focused on fast deployment and hosting of frontend applications.'
          }
        ]
      },
      {
        name: 'Data',
        links: [
          {
            id: 47,
            title: 'Google Search Console',
            url: 'https://search.google.com/search-console',
            description: 'Monitor your website\'s presence in Google Search results and track SEO performance.'
          },
          {
            id: 48,
            title: 'Google Analytics',
            url: 'https://analytics.google.com/analytics/web/',
            description: 'Web traffic and user behavior data analysis tool.'
          },
          {
            id: 49,
            title: 'Bing Webmaster',
            url: 'https://www.bing.com/webmasters/about',
            description: 'Microsoft\'s search engine optimization and site management tool.'
          }
        ]
      },
      {
        name: 'Competitor research tools',
        links: [
          {
            id: 50,
            title: 'AITDK',
            url: 'https://aitdk.com',
            description: 'AI tool navigation site, categorizing and organizing new AI resource tools.'
          },
          {
            id: 51,
            title: 'Ahrefs',
            url: 'https://ahrefs.com/free-seo-tools',
            description: 'Provides backlink checking, keyword generation and free SEO functionality.'
          },
          {
            id: 52,
            title: 'SEMrush',
            url: 'https://semrush.com',
            description: 'Comprehensive SEO competition analysis platform, covering keyword research and advertising insights.'
          }
        ]
      }
    ]
  },
  {
    name: 'Learning Resources',
    subcategories: [
      {
        name: 'Documentation',
        links: [
          {
            id: 5,
            title: 'MDN Web Docs',
            url: 'https://developer.mozilla.org',
            description: 'Resources for developers, by developers'
          },
          {
            id: 6,
            title: 'Vue.js Documentation',
            url: 'https://vuejs.org',
            description: 'Official Vue.js documentation and guides'
          }
        ]
      },
      {
        name: 'Learning Platforms',
        links: [
          {
            id: 7,
            title: 'freeCodeCamp',
            url: 'https://www.freecodecamp.org',
            description: 'Learn to code for free'
          },
          {
            id: 8,
            title: 'Codecademy',
            url: 'https://www.codecademy.com',
            description: 'Interactive platform for learning to code'
          }
        ]
      }
    ]
  },
  {
    name: 'Image Resources',
    subcategories: [
      {
        name: 'SVG Resources',
        links: [
          {
            id: 9,
            title: 'Freepik',
            url: 'https://www.freepik.com/',
            description: 'A platform offering a vast collection of free and premium graphic resources like vectors, illustrations, and photos for designers'
          },
          {
            id: 10,
            title: 'SVG',
            url: 'https://www.svg.com/',
            description: 'Games, gaming culture, and pop culture, offering insights and updates for both casual and avid gamers.'
          },
          {
            id: 11,
            title: 'Flaticon',
            url: 'https://www.flaticon.com/',
            description: 'A website providing millions of vector icons in various formats, useful for both personal and commercial design projects.'
          },
          {
            id: 12,
            title: 'Vecteezy',
            url: 'https://www.vecteezy.com/',
            description: 'A site offering free and premium vector art, photos, and videos for creative projects with a user-friendly license structure.'
          },
          {
            id: 13,
            title: 'SVG Silh',
            url: 'https://svgsilh.com/',
            description: 'A free resource site for downloading high-quality SVG images and icons, all released under Creative Commons CC0 for unrestricted use.'
          }
        ]
      },
      {
        name: 'Wallpaper',
        links: [
          {
            id: 14,
            title: 'Wallhaven',
            url: 'https://wallhaven.cc/',
            description: 'A high-quality wallpaper sharing community with extensive filtering options and regular updates'
          },
          {
            id: 15,
            title: 'Unsplash',
            url: 'https://unsplash.com/',
            description: 'Beautiful, free images and photos that you can download and use for any project'
          },
          {
            id: 16,
            title: 'Pexels',
            url: 'https://www.pexels.com/',
            description: 'Free stock photos, royalty free images & videos shared by creators'
          },
          {
            id: 17,
            title: 'WallpaperAccess',
            url: 'https://wallpaperaccess.com/',
            description: 'Free HD wallpapers for your computer, smartphone, or tablet with easy download options'
          },
          {
            id: 18,
            title: 'DeviantArt',
            url: 'https://www.deviantart.com/search?q=wallpaper',
            description: 'The largest online art gallery and community, featuring unique wallpapers created by artists worldwide'
          }
        ]
      },
      {
        name: 'Photographs',
        links: [
          {
            id: 19,
            title: '500px',
            url: 'https://500px.com',
            description: 'A platform for photographers to showcase, share, and sell their high-quality images and photography.'
          },
          {
            id: 20,
            title: 'Flickr',
            url: 'https://flickr.com',
            description: 'A popular image and video hosting platform where photographers can share and organize their photos in albums.'
          },
          {
            id: 21,
            title: 'Behance Photography Gallery',
            url: 'https://behance.net/galleries/photography',
            description: 'A portfolio platform where creative professionals, including photographers, display their work for potential clients and collaborators.'
          },
          {
            id: 22,
            title: 'EyeEm',
            url: 'https://eyeem.com',
            description: 'A marketplace for authentic stock photography, offering a curated collection of images from photographers worldwide.'
          },
          {
            id: 23,
            title: 'Getty Images',
            url: 'https://gettyimages.com',
            description: 'A well-known provider of high-quality stock photos, editorial images, and creative visuals for commercial use.'
          }
        ]
      },
      {
        name: 'Memes',
        links: [
          {
            id: 24,
            title: 'Giphy',
            url: 'https://giphy.com',
            description: 'A platform for discovering, creating, and sharing GIFs to express emotions and ideas in digital communication.'
          },
          {
            id: 25,
            title: 'Tenor',
            url: 'https://tenor.com',
            description: 'A search engine and platform for sharing animated GIFs across various messaging and social media platforms.'
          },
          {
            id: 26,
            title: 'Imgflip',
            url: 'https://imgflip.com',
            description: 'A website for creating and sharing memes, GIFs, and other fun image-based content.'
          },
          {
            id: 27,
            title: 'Emojipedia',
            url: 'https://emojipedia.org',
            description: 'A comprehensive resource for emoji meanings, updates, and news about the evolving emoji language.'
          },
          {
            id: 28,
            title: 'Know Your Meme',
            url: 'https://knowyourmeme.com',
            description: 'A website that tracks and documents internet memes, viral content, and trends in digital culture.'
          }
        ]
      },
      {
        name: 'Image Hosting Tools',
        links: [
          {
            id: 29,
            title: 'ImgBB',
            url: 'https://imgbb.com',
            description: 'A free image hosting platform that allows users to upload and share images online.'
          },
          {
            id: 30,
            title: 'Imgur',
            url: 'https://imgur.com',
            description: 'A popular image-sharing and hosting platform known for viral content and a supportive community.'
          },
          {
            id: 31,
            title: 'Cloudinary',
            url: 'https://cloudinary.com',
            description: 'A cloud-based media management platform for uploading, storing, optimizing, and delivering images and videos.'
          },
          {
            id: 32,
            title: 'SM.MS',
            url: 'https://sm.ms',
            description: 'A simple, free image hosting service for uploading and sharing images with direct links.'
          },
          {
            id: 33,
            title: 'Postimages',
            url: 'https://postimages.org',
            description: 'An image hosting site that provides permanent links for sharing images on social media and message boards.'
          }
        ]
      }
    ]
  },
  {
    name: 'Templates',
    subcategories: [
      {
        name: 'Blog and Project Templates',
        links: [
          {
            id: 53,
            title: 'Rin',
            url: 'https://github.com/openRin/Rin',
            description: 'Dynamic blog template powered by Cloudflare'
          },
          {
            id: 54,
            title: 'Next.js 15 Boilerplate',
            url: 'https://github.com/ixartz/Next-js-Boilerplate',
            description: 'Next.js 15 project template'
          },
          {
            id: 55,
            title: 'SaaS Boilerplate',
            url: 'https://github.com/ixartz/SaaS-Boilerplate',
            description: 'SaaS project template'
          },
          {
            id: 56,
            title: 'Next.js Landing Page Template',
            url: 'https://github.com/ixartz/Next-JS-Landing-Page-Starter-Template',
            description: 'Next.js landing page template'
          },
          {
            id: 57,
            title: 'Astro Boilerplate',
            url: 'https://github.com/ixartz/Astro-boilerplate',
            description: 'Astro project template'
          },
          {
            id: 58,
            title: 'Multi-purpose Landing Page Template',
            url: 'https://github.com/weijunext/landing-page-boilerplate',
            description: 'Landing page template suitable for various scenarios'
          },
          {
            id: 59,
            title: 'Weekly Blog Template',
            url: 'https://github.com/weijunext/new-blog',
            description: 'Blog template suitable for weekly content publishing'
          }
        ]
      },
      {
        name: 'SaaS and Application Templates',
        links: [
          {
            id: 60,
            title: 'Viggle AI WebUI',
            url: 'https://github.com/ai-aigc-studio/Viggle-AI-WebUI',
            description: 'Web interface template for AI applications'
          },
          {
            id: 61,
            title: 'Gapis Money',
            url: 'https://github.com/weijunext/gapis.money?tab=readme-ov-file',
            description: 'Fintech application template'
          },
          {
            id: 62,
            title: 'Huntscreens',
            url: 'https://github.com/daimajia/huntscreens',
            description: 'Screenshot management application template'
          },
          {
            id: 63,
            title: 'Smart Excel AI',
            url: 'https://github.com/weijunext/smart-excel-ai',
            description: 'Excel intelligent processing application template'
          },
          {
            id: 64,
            title: 'NextJS VPS Example',
            url: 'https://github.com/ashleyrudland/nextjs_vps',
            description: 'NextJS VPS deployment example template'
          },
          {
            id: 65,
            title: 'Change Hairstyle AI',
            url: 'https://github.com/Pwntus/change-hairstyle-ai',
            description: 'AI hairstyle transformation application template'
          },
          {
            id: 66,
            title: 'Green Screen Creator',
            url: 'https://github.com/replicate/green-screen-creator',
            description: 'Green screen video processing application template'
          }
        ]
      },
      {
        name: 'Full-stack SaaS Frameworks',
        links: [
          {
            id: 67,
            title: 'Wasp Open SaaS',
            url: 'https://github.com/wasp-lang/open-saas/',
            description: 'Open-source SaaS application framework with user authentication, payment, and management features'
          },
          {
            id: 68,
            title: 'JustShip',
            url: 'https://github.com/ocluf/justship',
            description: 'Full-stack framework for quickly building and deploying SaaS applications'
          },
          {
            id: 69,
            title: 'Supanuxt SaaS',
            url: 'https://github.com/JavascriptMick/supanuxt-saas',
            description: 'SaaS starter template based on Nuxt and Supabase'
          },
          {
            id: 70,
            title: 'SaasFly',
            url: 'https://github.com/saasfly/saasfly',
            description: 'Modern SaaS application development framework with multi-tenant and billing features'
          },
          {
            id: 71,
            title: 'ShipFast Typescript',
            url: 'https://github.com/mundane799699/xlike-web',
            description: 'TypeScript full-stack SaaS framework with authentication and payment integration'
          }
        ]
      },
      {
        name: 'Navigation Site Templates',
        links: [
          {
            id: 72,
            title: 'FRE123',
            url: 'https://fre123.com/',
            description: 'Open-source navigation website template with custom category and link management support'
          },
          {
            id: 73,
            title: 'Oss Gallery',
            url: 'https://oss.gallery/',
            description: 'Open-source project showcase navigation template, beautiful and easily customizable'
          },
          {
            id: 74,
            title: 'WebStack',
            url: 'https://webstack.cc/cn/index.html',
            description: 'Classic website navigation open-source project with multiple theme support'
          },
          {
            id: 75,
            title: 'AI Tools Directory',
            url: 'https://tap4.ai/',
            description: 'AI tool navigation website template with search and categorization features'
          },
          {
            id: 76,
            title: 'GPTs Store',
            url: 'https://gpts.works/',
            description: 'GPT model showcase navigation template with rating and review functionality'
          },
          {
            id: 77,
            title: 'UI Lib Picker',
            url: 'https://github.com/ddahan/ui-libs',
            description: 'UI component library navigation template helping developers choose suitable UI libraries'
          },
          {
            id: 78,
            title: 'DokeyAI Data',
            url: 'https://github.com/DokeyAI/dokeai-data',
            description: 'AI dataset navigation template with dataset categorization and search capabilities'
          }
        ]
      }
    ]
  }
])

const filteredCategories = computed(() => {
  if (!searchQuery.value) return categories.value

  return categories.value.map(category => ({
    ...category,
    subcategories: category.subcategories.map(subcategory => ({
      ...subcategory,
      links: subcategory.links.filter(link =>
        link.title.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
        link.description.toLowerCase().includes(searchQuery.value.toLowerCase())
      )
    })).filter(subcategory => subcategory.links.length > 0)
  })).filter(category => category.subcategories.some(sub => sub.links.length > 0))
})
</script>