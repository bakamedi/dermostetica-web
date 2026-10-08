<script setup lang="ts">
import { computed, onMounted, onUnmounted, ref } from 'vue'

const menuOpen = ref(false)
const activeFaq = ref<number | null>(0)
const selectedCategory = ref<'all' | 'injectables' | 'laser' | 'skin'>('all')
const activeStoryModal = ref<number | null>(null)
const isScrolled = ref(false)
let scrollTicking = false

function handleScroll() {
  if (!scrollTicking) {
    requestAnimationFrame(() => {
      const scrolled = window.scrollY > 20
      if (isScrolled.value !== scrolled) {
        isScrolled.value = scrolled
      }
      scrollTicking = false
    })
    scrollTicking = true
  }
}

let scrollObserver: IntersectionObserver | null = null

// Chea-Inspired Fullscreen Hero Slider State
const urlParams = typeof window !== 'undefined' ? new URLSearchParams(window.location.search) : null
const initialSlideParam = urlParams?.get('slide') ? parseInt(urlParams.get('slide')!) : 0
const currentHeroSlide = ref(isNaN(initialSlideParam) ? 0 : initialSlideParam)
let heroSliderTimer: ReturnType<typeof setInterval> | null = null
const HERO_SLIDE_DURATION = 5000

function nextHeroSlide(resetTimer = false) {
  currentHeroSlide.value = (currentHeroSlide.value + 1) % heroSlides.length
  if (resetTimer) startHeroSlider()
}

function prevHeroSlide(resetTimer = false) {
  currentHeroSlide.value = (currentHeroSlide.value - 1 + heroSlides.length) % heroSlides.length
  if (resetTimer) startHeroSlider()
}

function goToHeroSlide(index: number) {
  currentHeroSlide.value = index
  startHeroSlider()
}

function startHeroSlider() {
  stopHeroSlider()
  heroSliderTimer = setInterval(() => {
    nextHeroSlide(false)
  }, HERO_SLIDE_DURATION)
}

function stopHeroSlider() {
  if (heroSliderTimer) {
    clearInterval(heroSliderTimer)
    heroSliderTimer = null
  }
}

let touchStartX = 0
let touchEndX = 0

function handleSliderTouchStart(e: TouchEvent) {
  if (e.changedTouches && e.changedTouches[0]) {
    touchStartX = e.changedTouches[0].screenX
  }
}

function handleSliderTouchEnd(e: TouchEvent) {
  if (e.changedTouches && e.changedTouches[0]) {
    touchEndX = e.changedTouches[0].screenX
  }
  if (touchStartX - touchEndX > 50) {
    nextHeroSlide(true)
  } else if (touchEndX - touchStartX > 50) {
    prevHeroSlide(true)
  }
}

function handleSliderKeydown(e: KeyboardEvent) {
  if (e.key === 'ArrowRight') nextHeroSlide(true)
  if (e.key === 'ArrowLeft') prevHeroSlide(true)
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll, { passive: true })
  handleScroll()
  startHeroSlider()
  window.addEventListener('keydown', handleSliderKeydown)

  // Silky Scroll Reveal Observer with dream blur and cascading stagger
  scrollObserver = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          entry.target.classList.add('is-revealed')
          if (
            entry.target.classList.contains('chea-counter-strip') ||
            entry.target.querySelector?.('.chea-counter-grid')
          ) {
            runStatsCounter()
          }
        }
      })
    },
    { threshold: 0.08, rootMargin: '0px 0px -40px 0px' }
  )

  setTimeout(() => {
    document
      .querySelectorAll('.reveal-on-scroll, .reveal-stagger, .reveal-card, .chea-counter-strip')
      .forEach((el) => {
        scrollObserver?.observe(el)
      })
  }, 100)
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
  window.removeEventListener('keydown', handleSliderKeydown)
  stopHeroSlider()
  scrollObserver?.disconnect()
})

// Official links for DermoSTETICA
const bookingUrl = 'https://taplink.cc/dermostetica'
const instagramUrl = 'https://www.instagram.com/dermostetica_ec/?hl=es-la'
const doctorInstagramUrl = 'https://www.instagram.com/dra_evelyngonzalez/'
const whatsappNumber1 = '593979310507'
const whatsappNumber2 = '593959832254'
const mapsUrl = 'https://maps.google.com/?q=Salinas+Ecuador+Supermaxi+Carlos+Espinoza+Larrea'

function getWhatsAppUrl(text: string, phone: string = whatsappNumber1) {
  return `https://wa.me/${phone}?text=${encodeURIComponent(text)}`
}

const defaultWhatsAppUrl = getWhatsAppUrl(
  'Hola DermoSTETICA, me gustaría agendar una valoración médica personalizada en su clínica de Salinas.'
)
const secondaryWhatsAppUrl = getWhatsAppUrl(
  'Hola DermoSTETICA, deseo consultar disponibilidad de horarios para una cita en Salinas.',
  whatsappNumber2
)

// Chea-Style Fullscreen Hero Slides Data
const heroSlides = [
  {
    id: 1,
    tag: 'MEDICINA ESTÉTICA Y LÁSER · SALINAS',
    titleLine1: 'TU BELLEZA, EN',
    titleItalic: 'Armonía Contigo',
    titleLine2: 'DE ALTA PRECISIÓN',
    description:
      'Cuidamos de ti con más de 10 años de experiencia médica. Procedimientos no invasivos, tecnología láser avanzada y un criterio estético que celebra tu belleza natural.',
    primaryBtn: {
      text: 'Agenda tu Valoración',
      url: defaultWhatsAppUrl,
      target: '_blank',
    },
    secondaryBtn: {
      text: 'Descubre los Tratamientos',
      url: '#tratamientos',
      target: '_self',
    },
    bgImage:
      'https://images.unsplash.com/photo-1616683693504-3ea7e9ad6fec?auto=format&fit=crop&w=2000&q=90',
    bgAlt: 'Paciente radiante y cuidada en DermoSTETICA Salinas',
    credentials: [
      { text: '+10 Años de Experiencia Médica', badge: '✦' },
      { text: 'Inyector Certificado BOTOX & Fillers', badge: '✦' },
      { text: 'Salinas, Ecuador (Frente a Supermaxi)', badge: '✦' },
    ],
  },
  {
    id: 2,
    tag: 'TECNOLOGÍA MÉDICA DE ALTA ENERGÍA',
    titleLine1: 'EXCELENCIA EN CABINA &',
    titleItalic: 'Lifting sin Cirugía',
    titleLine2: 'CON LÁSER HARMONY XL',
    description:
      'Tratamientos fotoacústicos no ablativos y remodelación dérmica profunda que activan colágeno nuevo con confort absoluto y cero tiempo de recuperación.',
    primaryBtn: {
      text: 'Explorar Carta de Protocolos',
      url: '#protocolos',
      target: '_self',
    },
    secondaryBtn: {
      text: 'Nuestros Tratamientos',
      url: '#tratamientos',
      target: '_self',
    },
    bgImage:
      'https://images.unsplash.com/photo-1570172619644-dfd03ed5d881?auto=format&fit=crop&w=2000&q=90',
    bgAlt: 'Cabina de tecnología láser de remodelación dérmica en Salinas',
    features: [
      {
        icon: '✦',
        title: 'Armonización Facial',
        sub: 'Toxina Botulínica & Labios',
        desc: 'Simetría anatómica, perfilado labial ruso y contorno mandibular definido sin bisturí.',
        link: '#scanner',
      },
      {
        icon: '✦',
        title: 'Láser ClearLift™',
        sub: 'Alma Harmony XL Q-Switched',
        desc: 'Lifting mecánico fotoacústico en dermis reticular (3.0 mm) sin agredir la piel ni causar dolor.',
        link: '#protocolos',
      },
      {
        icon: '✦',
        title: 'PB Serum Lipoenzimas',
        sub: 'Definición & Firmeza',
        desc: 'Disolución enzimática de grasa submentoniana y tensado del cuello sin cirugía.',
        link: '#protocolos',
      },
    ],
  },
  {
    id: 3,
    tag: 'DIRECCIÓN MÉDICA ESPECIALIZADA · SALINAS',
    titleLine1: 'CIENCIA, ÉTICA &',
    titleItalic: 'Belleza Consciente',
    titleLine2: 'EN LA COSTA ECUATORIANA',
    description:
      '«No cambiamos quién eres; restituimos la frescura, el equilibrio anatómico y la armonía que el paso del tiempo o el cansancio desdibujan.»',
    primaryBtn: {
      text: 'Hablar con la Dra. Evelyn',
      url: defaultWhatsAppUrl,
      target: '_blank',
    },
    secondaryBtn: {
      text: 'Conoce Nuestra Sede Salinas',
      url: '#clinica',
      target: '_self',
    },
    bgImage:
      'https://images.unsplash.com/photo-1629909613654-28e377c37b09?auto=format&fit=crop&w=2000&q=90',
    bgAlt: 'Instalaciones y cabina médica de DermoSTETICA en Salinas',
    doctorCard: {
      quote:
        '«Cada rostro es una obra única de proporciones y emociones. Cuidamos cada detalle para realzar tu propia luz.»',
      name: 'Dra. Evelyn González',
      role: 'Directora Médica · Especialista en Medicina Estética & Láser',
      location: 'Salinas, Ecuador (Av. Carlos Espinoza Larrea frente a Supermaxi)',
    },
  },
]

// 11th Anniversary Packages Data from Official Clinic Promotions
const selectedAnniversaryCategory = ref<'all' | 'regen' | 'lifting' | 'skin'>('all')

const anniversaryPackages = [
  {
    id: 'exopdrn',
    category: 'regen' as const,
    name: 'EXOPDRN',
    techTag: 'TECNOLOGÍA NANOPORE & LED',
    subtitle: 'Regeneración celular y glow intensivo con inductores PDRN',
    sessions: '3 Sesiones',
    price: '$405',
    originalPrice: '$540',
    protocol: '3 sesiones completas de microneedling Nanopore con cóctel exosomal regenerador PDRN.',
    benefit: 'Reactivación profunda de colágeno, textura dérmica aterciopelada y luminosidad inmediata.',
    gift: 'FOTOAGE LED en todas tus sesiones (Valorizado en $150 de cortesía)',
    duration: '45 min / sesión',
    downtime: 'Leve eritema 12-24h',
    image:
      'https://images.unsplash.com/photo-1512290923902-8a9f81dc236c?auto=format&fit=crop&w=800&q=85',
    alt: 'Tratamiento facial EXOPDRN Nanopore y Fotoage LED en DermoStetica',
    waText: 'Hola DermoSTETICA, me interesa agendar el paquete de 11 Aniversario EXOPDRN ($405 por 3 sesiones).',
  },
  {
    id: 'laser-nir',
    category: 'lifting' as const,
    name: 'LÁSER NIR EXPERIENCE',
    techTag: 'ALMA HARMONY XL & EXILIS',
    subtitle: 'Lifting fotoacústico y remodelación dérmica de máxima firmeza',
    sessions: '3 Terapias',
    price: '$499',
    originalPrice: '$680',
    protocol: 'Protocolo dual con radiofrecuencia médica Exilis + láser infrarrojo cercano NIR.',
    benefit: 'Tensado inmediato del óvalo facial, cuello y escote sin dolor ni tiempo de baja.',
    included: 'Análisis facial FOCUSKIN + 1 sesión de Exilis Facial completa (rostro + escote)',
    duration: '60 min / sesión',
    downtime: 'Cero tiempo de reposo',
    image:
      'https://images.unsplash.com/photo-1570172619644-dfd03ed5d881?auto=format&fit=crop&w=800&q=85',
    alt: 'Tratamiento de remodelación dérmica Láser NIR Experience en cabina',
    waText: 'Hola DermoSTETICA, deseo reservar la promoción Láser NIR Experience ($499 por 3 terapias).',
  },
  {
    id: 'radiesse-lift',
    category: 'lifting' as const,
    name: 'RADIESSE LIFT',
    techTag: 'HIDROXIAPATITA DE CALCIO',
    subtitle: 'Bioestimulador que potencia la firmeza, elasticidad y calidad cutánea',
    sessions: '1 Sesión',
    price: '$499',
    originalPrice: '$650',
    protocol: 'Infiltración médica vectorizada en plano subdérmico por la Dra. Evelyn González.',
    benefit: 'Efecto tensor biológico progresivo, regeneración de elastina y soporte estructural duradero.',
    tag: 'Tarifa conmemorativa especial por el 11.º Aniversario',
    duration: '40 min',
    downtime: 'Incorporación inmediata',
    image:
      'https://images.unsplash.com/photo-1508214751196-bcfd4ca60f91?auto=format&fit=crop&w=800&q=85',
    alt: 'Bioestimulador de colágeno Radiesse Lift en Salinas',
    waText: 'Hola DermoSTETICA, me interesa el tratamiento Radiesse Lift ($499 precio especial de aniversario).',
  },
  {
    id: 'fotoage',
    category: 'regen' as const,
    name: 'FOTOAGE SKIN RADIANCE',
    techTag: 'FOTOTERAPIA LLLT MÉDICA',
    subtitle: 'Rejuvenecimiento, despigmentación, control de acné y rosácea',
    sessions: '4 Terapias',
    price: '$280',
    originalPrice: '$380',
    protocol: '4 terapias fotoactivadas con protocolos individualizados de luz roja, azul y amarilla.',
    benefit: 'Unificación del tono facial, reducción de rojeces y calma dérmica sin irritación.',
    gift: 'Diagnóstico biométrico computarizado FOCUSKIN de cortesía',
    duration: '35 min / sesión',
    downtime: 'No invasivo / Efecto glow',
    image:
      'https://images.unsplash.com/photo-1515377905703-c4788e51af15?auto=format&fit=crop&w=800&q=85',
    alt: 'Tratamiento de fototerapia Fotoage Skin Radiance en DermoStetica',
    waText: 'Hola DermoSTETICA, deseo consultar sobre el paquete Fotoage Skin Radiance ($280 por 4 terapias).',
  },
  {
    id: 'emfusion-exp',
    category: 'skin' as const,
    name: 'EMFUSION EXPERIENCE',
    techTag: 'ELECTROPORACIÓN TRANSDÉRMICA',
    subtitle: 'Tecnología avanzada que potencia la hidratación profunda sin agujas',
    sessions: '1 Terapia',
    price: '$108',
    originalPrice: '$160',
    protocol: 'Infusión molecular de ácido hialurónico biocompatible, antioxidantes y péptidos.',
    benefit: 'Recuperación de la barrera cutánea, sensación de frescura absoluta y luminosidad radiante.',
    gift: 'Diagnóstico biométrico computarizado FOCUSKIN de cortesía',
    duration: '45 min',
    downtime: 'Cero reposo / Frescura total',
    image:
      'https://images.unsplash.com/photo-1522337360788-8b13dee7a37e?auto=format&fit=crop&w=800&q=85',
    alt: 'Hidratación profunda transdérmica EMFUSION Experience',
    waText: 'Hola DermoSTETICA, deseo reservar la sesión de EMFUSION Experience ($108).',
  },
  {
    id: 'dermopremium-skin',
    category: 'skin' as const,
    name: 'DERMOPREMIUM SKIN',
    techTag: 'HIGIENE PROFUNDA & SENSORIAL',
    subtitle: 'Limpieza facial médica con productos Premium · Piel limpia y fresca',
    sessions: 'Special Edition',
    price: '$55',
    originalPrice: '$85',
    protocol: 'Limpieza dérmica integral con espátula ultrasónica, alta frecuencia y mascarilla hidratante.',
    benefit: 'Eliminación suave de impurezas y células muertas dejando un aspecto pulcro y descansado.',
    gift: 'Incluye copa de champagne de bienvenida durante tu cita',
    duration: '50 min',
    downtime: 'Piel fresca e hidratada',
    image:
      'https://images.unsplash.com/photo-1598256989800-fe5f95da9787?auto=format&fit=crop&w=800&q=85',
    alt: 'Limpieza facial DermoPremium Skin con champagne en Salinas',
    waText: 'Hola DermoSTETICA, deseo agendar la limpieza DermoPremium Skin ($55 con copa de champagne).',
  },
]

const filteredAnniversaryPackages = computed(() => {
  if (selectedAnniversaryCategory.value === 'all') return anniversaryPackages
  return anniversaryPackages.filter((p) => p.category === selectedAnniversaryCategory.value)
})

// Instagram Highlights based on the official @dermostetica_ec profile (Photo covers, no clip-art icons)
const stories = [
  {
    id: 1,
    type: 'schedule',
    title: 'Horarios',
    subtitle: 'Atención previa cita',
    tag: 'Salinas',
    summary: 'Horarios de consulta y agenda',
    content:
      'Atendemos de Lunes a Sábado bajo previa cita para garantizar un espacio exclusivo, puntual y sin esperas en nuestra clínica de Salinas.',
    hoursWeekday: '09H00 a 19H00',
    hoursSaturday: '09H00 a 16H00',
    phone: '095 983 2254',
    location: 'Av. Carlos Espinoza Larrea, frente a Supermaxi, Salinas',
    coverImage:
      'https://images.unsplash.com/photo-1629909613654-28e377c37b09?auto=format&fit=crop&w=400&q=80',
    image:
      'https://images.unsplash.com/photo-1629909613654-28e377c37b09?auto=format&fit=crop&w=900&q=85',
  },
  {
    id: 2,
    type: 'anniversary',
    title: '11 Aniversario',
    subtitle: '11 tratamientos con regalos',
    tag: 'Promociones',
    summary: 'Celebramos nuestros 11 años acompañándote',
    content:
      'Celebramos nuestros años acompañándote con 11 tratamientos más elegidos con beneficios exclusivos y regalos especiales.',
    packages: anniversaryPackages,
    coverImage:
      'https://images.unsplash.com/photo-1513151233558-d860c5398176?auto=format&fit=crop&w=400&q=80',
    image:
      'https://images.unsplash.com/photo-1512290923902-8a9f81dc236c?auto=format&fit=crop&w=900&q=85',
  },
  {
    id: 3,
    type: 'treatment',
    title: 'Labios',
    subtitle: 'Perfilado & volumen',
    tag: 'Ácido Hialurónico',
    summary: 'Técnica de hidratación y diseño',
    content:
      'Realce armónico con ácido hialurónico de última generación: definimos el arco de cupido, hidratamos la mucosa labial y aportamos un volumen delicado y elegante.',
    hours: 'Procedimiento de 30 a 45 minutos',
    location: 'Resultados inmediatos y naturales',
    coverImage:
      'https://images.unsplash.com/photo-1588516903720-8ceb67f9ef84?auto=format&fit=crop&w=400&q=80',
    image:
      'https://images.unsplash.com/photo-1588516903720-8ceb67f9ef84?auto=format&fit=crop&w=900&q=85',
  },
  {
    id: 4,
    type: 'treatment',
    title: 'Rinomodelación',
    subtitle: 'Armonización sin cirugía',
    tag: 'Perfil facial',
    summary: 'Corrección estética en una sesión',
    content:
      'Corrección del dorso nasal, rectificación de curvas y elevación sutil de la punta mediante ácido hialurónico de alta densidad, sin quirófano ni reposo.',
    hours: 'Procedimiento de 30 minutos',
    location: 'Sin postoperatorio quirúrgico',
    coverImage:
      'https://images.unsplash.com/photo-1596755389378-c31d21fd1273?auto=format&fit=crop&w=400&q=80',
    image:
      'https://images.unsplash.com/photo-1596755389378-c31d21fd1273?auto=format&fit=crop&w=900&q=85',
  },
  {
    id: 5,
    type: 'treatment',
    title: 'Botox Facial',
    subtitle: 'Inyector certificado',
    tag: 'Toxina Botulínica',
    summary: 'Prevención y suavizado de arrugas',
    content:
      'Aplicación experta por la Dra. Evelyn González. Suaviza líneas en frente, entrecejo y patas de gallo manteniendo tu frescura y expresividad sin efecto congelado.',
    hours: 'Duración: 4 a 6 meses de duración',
    location: 'Productos oficiales con registro sanitario',
    coverImage:
      'https://images.unsplash.com/photo-1570172619644-dfd03ed5d881?auto=format&fit=crop&w=400&q=80',
    image:
      'https://images.unsplash.com/photo-1570172619644-dfd03ed5d881?auto=format&fit=crop&w=900&q=85',
  },
  {
    id: 6,
    type: 'treatment',
    title: 'Láser NIR',
    subtitle: 'Remodelación dérmica',
    tag: 'Harmony XL',
    summary: 'Lifting sin dolor ni tiempo de baja',
    content:
      'Lifting fotoacústico no ablativo con láser Alma Harmony XL ClearLift y NIR. Estimula nuevo colágeno en dermis profunda para máxima firmeza y luminosidad.',
    hours: 'Sesión de 45 minutos',
    location: 'Tecnología médica certificada',
    coverImage:
      'https://images.unsplash.com/photo-1512290923902-8a9f81dc236c?auto=format&fit=crop&w=400&q=80',
    image:
      'https://images.unsplash.com/photo-1512290923902-8a9f81dc236c?auto=format&fit=crop&w=900&q=85',
  },
  {
    id: 7,
    type: 'treatment',
    title: 'DermoPremium',
    subtitle: 'Limpieza + Champagne $55',
    tag: 'Edición Especial',
    summary: 'Higiene dérmica de alta gama',
    content:
      'Una experiencia de limpieza facial con productos Premium. Deja tu piel limpia, fresca y luminosa. Incluye análisis facial y copa de champagne.',
    hours: 'Precio Especial Aniversario: $55',
    location: 'Sede Salinas',
    coverImage:
      'https://images.unsplash.com/photo-1515377905703-c4788e51af15?auto=format&fit=crop&w=400&q=80',
    image:
      'https://images.unsplash.com/photo-1515377905703-c4788e51af15?auto=format&fit=crop&w=900&q=85',
  },
  {
    id: 8,
    type: 'treatment',
    title: 'FOCUSKIN 3D',
    subtitle: 'Diagnóstico digital',
    tag: 'Diagnóstico Facial',
    summary: 'Escaneo computarizado de alta definición',
    content:
      'Evaluación científica biométrica de poros, manchas dérmicas, daño solar y arrugas para definir tu tratamiento médico personalizado con exactitud.',
    hours: 'Consulta de 30 minutos',
    location: 'Análisis previo a tratamientos',
    coverImage:
      'https://images.unsplash.com/photo-1620916566398-39f1143ab7be?auto=format&fit=crop&w=400&q=80',
    image:
      'https://images.unsplash.com/photo-1620916566398-39f1143ab7be?auto=format&fit=crop&w=900&q=85',
  },
]

// Detailed services
const services = [
  {
    id: 'botox',
    category: 'injectables',
    name: 'Toxina Botulínica (Botox)',
    badge: 'Tratamiento Estrella',
    subtitle: 'Prevención y rejuvenecimiento de expresión',
    description:
      'Tratamiento de alta precisión para suavizar arrugas dinámicas en frente, entrecejo y patas de gallo. Respeta tu mímica facial para un aspecto descansado, terso y natural.',
    duration: '30 min',
    downtime: 'Inmediata incorporación',
    image:
      'https://images.unsplash.com/photo-1512290923902-8a9f81dc236c?auto=format&fit=crop&w=1000&q=85',
    alt: 'Tratamiento facial de rejuvenecimiento y toxina botulínica',
    waText: 'Hola Dra. Evelyn, deseo consultar sobre la aplicación de Botox en DermoSTETICA.',
  },
  {
    id: 'labios',
    category: 'injectables',
    name: 'Perfilado y Volumen de Labios',
    badge: 'Ácido Hialurónico',
    subtitle: 'Diseño sutil, definición e hidratación',
    description:
      'Aporte de hidratación profunda y volumen armónico con ácido hialurónico premium. Diseñado a la medida de la anatomía de tu rostro para unos labios simétricos y sensuales.',
    duration: '40 min',
    downtime: 'Leve inflamación 24-48h',
    image:
      'https://images.unsplash.com/photo-1598256989800-fe5f95da9787?auto=format&fit=crop&w=1000&q=85',
    alt: 'Perfilado y volumen de labios con ácido hialurónico',
    waText: 'Hola Dra. Evelyn, me interesa información sobre armonización y perfilado de labios.',
  },
  {
    id: 'rinomodelacion',
    category: 'injectables',
    name: 'Rinomodelación sin Cirugía',
    badge: 'Armonización Nasal',
    subtitle: 'Equilibrio de perfil en una sola sesión',
    description:
      'Corrección estética del caballete y elevación de la punta nasal mediante infiltraciones milimétricas de ácido hialurónico. Sin quirófano, sin tapones y con resultados inmediatos.',
    duration: '35 min',
    downtime: 'Sin reposo',
    image:
      'https://images.unsplash.com/photo-1508214751196-bcfd4ca60f91?auto=format&fit=crop&w=1000&q=85',
    alt: 'Rinomodelación y armonización estética de perfil',
    waText: 'Hola Dra. Evelyn, quisiera información sobre la rinomodelación sin cirugía.',
  },
  {
    id: 'armonizacion',
    category: 'injectables',
    name: 'Armonización Facial & Contornos',
    badge: 'Enfoque Integral',
    subtitle: 'Definición de mentón, mandíbula y pómulos',
    description:
      'Plan médico global que equilibra las proporciones del tercio medio e inferior: proyección de mentón, marcación mandibular y restitución de volúmenes en pómulos y ojeras.',
    duration: '60 min',
    downtime: 'Incorporación inmediata',
    image:
      'https://images.unsplash.com/photo-1534528741775-53994a69daeb?auto=format&fit=crop&w=1000&q=85',
    alt: 'Armonización facial estética y contornos mandibulares',
    waText: 'Hola Dra. Evelyn, deseo una valoración para armonización facial integral.',
  },
  {
    id: 'laser-harmony',
    category: 'laser',
    name: 'Láser Harmony XL · ClearLift & NIR',
    badge: 'Tecnología Láser',
    subtitle: 'Lifting no ablativo y remodelación de colágeno',
    description:
      'Plataforma láser médica de referencia mundial. Trata la laxitud cutánea, unifica el tono facial, reduce manchas y poros dilatados estimulando colágeno desde capas profundas.',
    duration: '45 min',
    downtime: 'Sin tiempo de recuperación',
    image:
      'https://images.unsplash.com/photo-1570172619644-dfd03ed5d881?auto=format&fit=crop&w=1000&q=85',
    alt: 'Tratamiento facial con Láser Harmony XL en cabina',
    waText: 'Hola DermoSTETICA, me gustaría conocer más sobre las sesiones de Láser Harmony.',
  },
  {
    id: 'emfusion',
    category: 'skin',
    name: 'EMFUSION & Hidratación Profunda',
    badge: 'Revitalización Celular',
    subtitle: 'Nutrición dérmica transdérmica sin agujas',
    description:
      'Infusión profunda de principios activos, vitaminas y péptidos regeneradores mediante energía electromagnética. Deja la piel luminosa, tersa y visiblemente oxigenada.',
    duration: '50 min',
    downtime: 'Ninguno / Efecto glow',
    image:
      'https://images.unsplash.com/photo-1522337360788-8b13dee7a37e?auto=format&fit=crop&w=1000&q=85',
    alt: 'Tratamiento facial EMFUSION de hidratación y luminosidad',
    waText: 'Hola, deseo agendar una sesión de hidratación profunda EMFUSION.',
  },
  {
    id: 'lipoenzimas',
    category: 'injectables',
    name: 'Lipoenzimas de Papada y Cuello',
    badge: 'Definición Cervicofacial',
    subtitle: 'Disolución de grasa localizada sin quirófano',
    description:
      'Enzimas biológicas recombinantes PB Serum (Lipasa, Hialuronidasa y Colagenasa) que reducen el tejido graso submentoniano y reafirman la piel para un cuello estilizado.',
    duration: '35 min',
    downtime: 'Leve edema transitorio',
    image:
      'https://images.unsplash.com/photo-1515377905703-c4788e51af15?auto=format&fit=crop&w=1000&q=85',
    alt: 'Tratamiento no invasivo de lipoenzimas para papada y cuello',
    waText: 'Hola Dra. Evelyn, me interesa información sobre el tratamiento de lipoenzimas para papada.',
  },
  {
    id: 'focuskin',
    category: 'skin',
    name: 'Diagnóstico Facial Digital FOCUSKIN',
    badge: 'Tecnología de Diagnóstico',
    subtitle: 'Análisis dérmico computarizado de alta precisión',
    description:
      'Evaluación científica de las capas de tu piel: poros, manchas ocultas por daño solar, arrugas y niveles de hidratación para crear tu plan de tratamiento 100% exacto.',
    duration: '30 min',
    downtime: 'No invasivo',
    image:
      'https://images.unsplash.com/photo-1620916566398-39f1143ab7be?auto=format&fit=crop&w=1000&q=85',
    alt: 'Diagnóstico de piel computarizado FOCUSKIN de alta definición',
    waText: 'Hola, me gustaría agendar un diagnóstico facial digital FOCUSKIN.',
  },
]

const filteredServices = computed(() => {
  if (selectedCategory.value === 'all') return services
  return services.filter((s) => s.category === selectedCategory.value)
})

const activeStory = computed(() => {
  if (activeStoryModal.value === null) return null
  return stories.find((s) => s.id === activeStoryModal.value) || null
})

const faqs = [
  {
    num: '01',
    question: '¿Por qué es indispensable la valoración previa con la Dra. Evelyn?',
    answer:
      'En DermoSTETICA cada paciente es único. Una valoración médica personalizada permite examinar las proporciones anatómicas de tu rostro, calidad dérmica, historial clínico y expectativas reales para garantizar un resultado armónico.',
    bullets: [
      'Evaluación personalizada de fisionomía y simetría facial',
      'Protocolos con rigor médico sin riesgo de sobrecorrección',
    ],
  },
  {
    num: '02',
    question: '¿Qué productos médicos y marcas oficiales utilizan?',
    answer:
      'Trabajamos exclusivamente con insumos de alta gama con registro sanitario internacional y nacional: BOTOX® de Allergan y ácidos hialurónicos con certificación FDA y Arcsa.',
    bullets: [
      'Ampollas y jeringas selladas abiertas en presencia del paciente',
      'Cero productos genéricos o sustancias de procedencia no comprobada',
    ],
  },
  {
    num: '03',
    question: '¿Cuánto tiempo dura el efecto del Botox y cuándo se ven los resultados?',
    answer:
      'Los primeros cambios comienzan a notarse entre el 3er y 5to día, alcanzando su resultado definitivo a los 14 días. La duración promedio oscila entre 4 y 6 meses según el metabolismo individual.',
    bullets: [
      'Efecto sutil que conserva totalmente tu mímica y expresividad',
      'Revisión y control de seguimiento a los 15 días en Salinas',
    ],
  },
  {
    num: '04',
    question: '¿La rinomodelación con ácido hialurónico sustituye una cirugía?',
    answer:
      'Es la alternativa médica no quirúrgica predilecta para rectificar el dorso nasal, suavizar gibas y elevar sutilmente la punta en una sesión ambulatoria de 30 minutos sin quirófano.',
    bullets: [
      'Resultados visibles al instante sin tapones ni baja laboral',
      'Duración prolongada de 12 a 18 meses con producto reabsorbible',
    ],
  },
  {
    num: '05',
    question: '¿Dónde está ubicada la clínica en Salinas y cómo agendar?',
    answer:
      'Atendemos con cita previa en Av. Carlos Espinoza Larrea, sector Las Conchas, frente a Supermaxi, Salinas. Brindamos atención privada y puntual para tu máxima comodidad.',
    bullets: [
      'Atención de Lunes a Sábado con agenda programada sin esperas',
      'Contacto directo vía WhatsApp al 0979310507 / 0959832254',
    ],
  },
]

const activeResultTab = ref(0)
const sliderPos = ref(50)
const selectedFacialZone = ref(0)

function setSliderPreset(pos: number) {
  sliderPos.value = pos
}

const resultCases = [
  {
    title: 'Perfilado & Hidratación de Labios',
    subtitle: 'Ácido Hialurónico Premium',
    tag: 'Labios Armónicos',
    description:
      'Definición del arco de cupido, contornos nítidos e hidratación profunda con volumen equilibrado. Realza la belleza labial respetando la simetría natural del rostro.',
    quote: '«Buscamos una proyección delicada y suave sin alterar tu expresión natural.»',
    doctor: 'Dra. Evelyn González',
    stats: [
      { label: 'Tiempo de sesión', value: '35 min' },
      { label: 'Duración estimada', value: '9 a 12 meses' },
      { label: 'Recuperación', value: 'Inmediata' },
    ],
    beforeImage:
      'https://images.unsplash.com/photo-1588516903720-8ceb67f9ef84?auto=format&fit=crop&w=900&q=85',
    afterImage:
      'https://images.unsplash.com/photo-1512290923902-8a9f81dc236c?auto=format&fit=crop&w=900&q=85',
    beforeLabel: 'Antes de la hidratación',
    afterLabel: 'Resultado armonizado con volumen',
    waText: 'Hola Dra. Evelyn, estuve viendo el resultado de perfilado de labios en su web y deseo una valoración.',
  },
  {
    title: 'Rinomodelación sin Cirugía',
    subtitle: 'Armonización de Perfil Nasal',
    tag: 'Perfil Rectificado',
    description:
      'Micro-infiltraciones de ácido hialurónico de alta reticulación para rectificar la giba dorsal y elevar la punta nasal en una sola consulta médica.',
    quote: '«Un perfil equilibrado y estilizado en 30 minutos sin tiempo de baja ni vendajes.»',
    doctor: 'Dra. Evelyn González',
    stats: [
      { label: 'Tiempo de sesión', value: '30 min' },
      { label: 'Duración estimada', value: '12 a 18 meses' },
      { label: 'Resultado', value: 'Inmediato en cabina' },
    ],
    beforeImage:
      'https://images.unsplash.com/photo-1596755389378-c31d21fd1273?auto=format&fit=crop&w=900&q=85',
    afterImage:
      'https://images.unsplash.com/photo-1616683693504-3ea7e9ad6fec?auto=format&fit=crop&w=900&q=85',
    beforeLabel: 'Perfil previo a la sesión',
    afterLabel: 'Perfil estilizado y armónico',
    waText: 'Hola Dra. Evelyn, me interesa información sobre el procedimiento de rinomodelación sin cirugía.',
  },
  {
    title: 'Toxina Botulínica (Botox Facial)',
    subtitle: 'Tercio Superior & Expresión Descansada',
    tag: 'Mirada Radiante',
    description:
      'Suavizado selectivo de líneas de expresión en frente, entrecejo y patas de gallo. Piel tersa y luminosa conservando total naturalidad gestual.',
    quote: '«El mejor procedimiento es el que resalta tu frescura sin que se note la intervención.»',
    doctor: 'Dra. Evelyn González',
    stats: [
      { label: 'Tiempo de sesión', value: '25 min' },
      { label: 'Duración estimada', value: '4 a 6 meses' },
      { label: 'Revisión médica', value: 'Día 14 incluida' },
    ],
    beforeImage:
      'https://images.unsplash.com/photo-1570172619644-dfd03ed5d881?auto=format&fit=crop&w=900&q=85',
    afterImage:
      'https://images.unsplash.com/photo-1522337360788-8b13dee7a37e?auto=format&fit=crop&w=900&q=85',
    beforeLabel: 'Líneas dinámicas marcadas',
    afterLabel: 'Piel lisa y mirada descansada',
    waText: 'Hola Dra. Evelyn, deseo una consulta para aplicación de Botox facial preventivo.',
  },
]

// Digital Facial Scanner & Anatomical Zones Data
const facialZones = [
  {
    id: 'frente',
    name: 'Tercio Superior · Frente y Entrecejo',
    shortName: 'Frente & Entrecejo',
    tag: 'Toxina Botulínica (Botox)',
    coords: { x: 50, y: 22 },
    indication: 'Líneas dinámicas de expresión, arrugas de ceño y frente.',
    treatment: 'Botox Facial Preventivo & Suavizante',
    mechanism: 'Relajación selectiva de la musculatura mímica conservando total expresividad.',
    duration: '25 min',
    downtime: 'Incorporación inmediata',
    results: 'Visibles a los 3-5 días (duración 4-6 meses)',
    waText: 'Hola Dra. Evelyn, estuve usando el escáner facial de su web y deseo una cita para Botox en tercio superior.',
  },
  {
    id: 'mirada',
    name: 'Zona Periocular · Patas de Gallo',
    shortName: 'Mirada Periocular',
    tag: 'Apertura de Mirada',
    coords: { x: 74, y: 35 },
    indication: 'Líneas finas periorbiculares al sonreír y sensación de mirada cansada.',
    treatment: 'Micro-Botox & Bioestimulación Periocular',
    mechanism: 'Alisado de micro-líneas y elevación sutil de la cola de la ceja para una mirada descansada.',
    duration: '20 min',
    downtime: 'Sin tiempo de baja',
    results: 'Mirada fresca y despejada respetando tu sonrisa',
    waText: 'Hola Dra. Evelyn, me interesa una valoración para arrugas perioculares y refrescamiento de mirada.',
  },
  {
    id: 'nariz',
    name: 'Perfil Nasal · Dorso y Punta',
    shortName: 'Rinomodelación',
    coords: { x: 50, y: 46 },
    indication: 'Giba o curvatura dorsal, ángulo nasolabial caído y asimetrías.',
    treatment: 'Rinomodelación con Ácido Hialurónico',
    mechanism: 'Micro-infiltraciones tridimensionales de alta precisión sin quirófano ni tapones.',
    duration: '30 min',
    downtime: 'Sin reposo quirúrgico',
    results: 'Efecto inmediato en cabina (duración 12-18 meses)',
    waText: 'Hola Dra. Evelyn, deseo consultar sobre rinomodelación sin cirugía en DermoSTETICA Salinas.',
  },
  {
    id: 'labios',
    name: 'Perfilado & Bermellón Labial',
    shortName: 'Labios & Arco Cupido',
    tag: 'Ácido Hialurónico Premium',
    coords: { x: 50, y: 64 },
    indication: 'Pérdida de hidratación, arrugas peribucales y contornos difusos.',
    treatment: 'Perfilado, Hidratación & Volumen Labial',
    mechanism: 'Diseño del arco de cupido y aporte de volumen armónico según tu estructura ósea.',
    duration: '35 min',
    downtime: 'Leve inflamación 24-48h',
    results: 'Labios simétricos, hidratados y sensuales (9-12 meses)',
    waText: 'Hola Dra. Evelyn, me gustaría agendar una cita para perfilado e hidratación de labios.',
  },
  {
    id: 'mandibula',
    name: 'Contorno Mandibular & Mentón',
    shortName: 'Marcación Mandibular',
    tag: 'Definición & Soporte',
    coords: { x: 27, y: 76 },
    indication: 'Pérdida de nitidez en la línea mandibular, mentón retraído o flacidez.',
    treatment: 'Marcación Mandibular & Proyección de Mentón',
    mechanism: 'Restitución de soporte estructural para estilizar la transición entre rostro y cuello.',
    duration: '45 min',
    downtime: 'Inmediata incorporación',
    results: 'Rostro definido y estilizado en una sola sesión',
    waText: 'Hola Dra. Evelyn, deseo una consulta para definición y marcación de contorno mandibular.',
  },
  {
    id: 'papada',
    name: 'Zona Submentoniana & Papada',
    shortName: 'Lipoenzimas de Papada',
    tag: 'Lipoenzimas Recombinantes',
    coords: { x: 50, y: 88 },
    indication: 'Adiposidad localizada submentoniana y pérdida del ángulo del cuello.',
    treatment: 'Lipoenzimas PB Serum (Lipasa + Colagenasa)',
    mechanism: 'Disolución selectiva de grasa localizada y compactación dérmica sin cirugía.',
    duration: '30 min',
    downtime: 'Leve edema transitorio 48h',
    results: 'Reducción notable de volumen y cuello esbelto',
    waText: 'Hola Dra. Evelyn, me interesa información sobre el tratamiento de lipoenzimas para papada.',
  },
  {
    id: 'dermis',
    name: 'Calidad Dérmica Integral (Mejillas y Piel)',
    shortName: 'Láser Harmony & Piel',
    tag: 'Láser Harmony XL · ClearLift',
    coords: { x: 74, y: 60 },
    indication: 'Poros dilatados, falta de tono, fotoenvejecimiento y tono irregular.',
    treatment: 'Láser Harmony XL ClearLift + NIR',
    mechanism: 'Remodelación de colágeno fotoacústica profunda sin agredir la capa superficial.',
    duration: '45 min',
    downtime: '0 días de recuperación / Efecto glow',
    results: 'Piel firme, poros cerrados y luminosidad médica',
    waText: 'Hola DermoSTETICA, quisiera información sobre sesiones de Láser Harmony XL para mi piel.',
  },
]

// Chea-Inspired Minimalist Counter Strip Data with live silky counter
const clinicStats = ref([
  {
    targetNumber: 10,
    prefix: '+',
    suffix: '',
    value: '+10',
    display: '+0',
    label: 'Años de Experiencia Médica',
    sub: 'Criterio anatómico estricto',
  },
  {
    targetNumber: 2500,
    prefix: '+',
    suffix: '',
    value: '+2,500',
    display: '+0',
    label: 'Pacientes Armonizados',
    sub: 'En Salinas y Costa ecuatoriana',
  },
  {
    targetNumber: 100,
    prefix: '',
    suffix: '%',
    value: '100%',
    display: '0%',
    label: 'Procedimientos No Invasivos',
    sub: 'Sin quirófano ni baja médica',
  },
  {
    targetNumber: 0,
    prefix: '',
    suffix: '',
    value: 'Salinas',
    display: 'Salinas',
    label: 'Clínica Médica Exclusiva',
    sub: 'Frente a Supermaxi',
  },
])

let statsCounted = false
function runStatsCounter() {
  if (statsCounted) return
  statsCounted = true
  const duration = 1600
  const startTime = performance.now()

  function updateCount(currentTime: number) {
    const elapsed = currentTime - startTime
    const progress = Math.min(elapsed / duration, 1)
    const ease = progress === 1 ? 1 : 1 - Math.pow(2, -10 * progress)

    clinicStats.value.forEach((stat) => {
      if (stat.targetNumber > 0) {
        const currentVal = Math.round(stat.targetNumber * ease)
        if (stat.targetNumber >= 1000) {
          stat.display = `${stat.prefix}${currentVal.toLocaleString()}${stat.suffix}`
        } else {
          stat.display = `${stat.prefix}${currentVal}${stat.suffix}`
        }
      } else {
        stat.display = stat.value
      }
    })

    if (progress < 1) {
      requestAnimationFrame(updateCount)
    } else {
      clinicStats.value[0].display = '+10'
      clinicStats.value[1].display = '+2,500'
      clinicStats.value[2].display = '100%'
      clinicStats.value[3].display = 'Salinas'
    }
  }

  requestAnimationFrame(updateCount)
}

// Wellora & Chea Inspired Interactive Treatment Protocols
const hoveredProtocol = ref<number | null>(null)

const editorialSessions = [
  {
    title: 'Toxina Botulínica Facial (Botox)',
    tag: 'Tercio Superior',
    duration: '30 min',
    price: 'Valoración Médica',
    icon: 'syringe',
    image:
      'https://images.unsplash.com/photo-1512290923902-8a9f81dc236c?auto=format&fit=crop&w=600&q=80',
    desc: 'Suavizado de líneas de expresión en frente, entrecejo y patas de gallo manteniendo tu expresividad y frescura natural.',
    waText: 'Hola Dra. Evelyn, me interesa agendar una cita para Toxina Botulínica (Botox) integral.',
  },
  {
    title: 'Perfilado & Hidratación Labial',
    tag: 'Ácido Hialurónico',
    duration: '35 min',
    price: 'Diseño Personalizado',
    icon: 'lips',
    image:
      'https://images.unsplash.com/photo-1598256989800-fe5f95da9787?auto=format&fit=crop&w=600&q=80',
    desc: 'Diseño anatómico del arco de cupido, contorno nítido e hidratación profunda con volumen equilibrado.',
    waText: 'Hola Dra. Evelyn, deseo consultar sobre el perfilado e hidratación labial con ácido hialurónico.',
  },
  {
    title: 'Rinomodelación sin Cirugía',
    tag: 'Armonización Nasal',
    duration: '30 min',
    price: 'Sesión Única',
    icon: 'profile',
    image:
      'https://images.unsplash.com/photo-1508214751196-bcfd4ca60f91?auto=format&fit=crop&w=600&q=80',
    desc: 'Corrección del dorso nasal y elevación sutil de la punta en cabina con resultados inmediatos sin tapones.',
    waText: 'Hola Dra. Evelyn, quisiera información sobre rinomodelación sin cirugía en Salinas.',
  },
  {
    title: 'Láser Harmony XL ClearLift',
    tag: 'Alma Lasers',
    duration: '45 min',
    price: 'Lifting Fotoacústico',
    icon: 'laser',
    image:
      'https://images.unsplash.com/photo-1570172619644-dfd03ed5d881?auto=format&fit=crop&w=600&q=80',
    desc: 'Micro-pulsos fotoacústicos que reactivan colágeno profundo. Sin dolor, sin descamación y con efecto glow.',
    waText: 'Hola DermoSTETICA, quisiera consultar sobre sesiones de Láser Harmony XL ClearLift en Salinas.',
  },
  {
    title: 'Lipoenzimas PB Serum Papada',
    tag: 'Definición Cervicofacial',
    duration: '35 min',
    price: 'Reducción Enzimática',
    icon: 'contour',
    image:
      'https://images.unsplash.com/photo-1515377905703-c4788e51af15?auto=format&fit=crop&w=600&q=80',
    desc: 'Enzimas biológicas recombinantes (Lipasa + Colagenasa) que disuelven grasa localizada y compactan la piel.',
    waText: 'Hola Dra. Evelyn, me interesa información sobre el tratamiento de lipoenzimas para papada.',
  },
  {
    title: 'EMFUSION Infusión Celular',
    tag: 'Electroporación Iónica',
    duration: '40 min',
    price: 'Revitalización Dérmica',
    icon: 'droplet',
    image:
      'https://images.unsplash.com/photo-1522337360788-8b13dee7a37e?auto=format&fit=crop&w=600&q=80',
    desc: 'Apertura de microcanales dérmicos para absorción profunda de ácido hialurónico puro y nutrientes regeneradores.',
    waText: 'Hola, me gustaría agendar una sesión de infusión transdérmica EMFUSION en DermoSTETICA.',
  },
  {
    title: 'Armonización Facial & Mentón',
    tag: 'Tercio Inferior',
    duration: '50 min',
    price: 'Proyección & Simetría',
    icon: 'sparkle',
    image:
      'https://images.unsplash.com/photo-1534528741775-53994a69daeb?auto=format&fit=crop&w=600&q=80',
    desc: 'Restitución de soporte en pómulos, ángulo mandibular y mentón para un contorno facial estilizado.',
    waText: 'Hola Dra. Evelyn, me interesa una valoración médica para armonización facial integral.',
  },
  {
    title: 'Diagnóstico Digital FOCUSKIN',
    tag: 'Dermo-Análisis Multiespectral',
    duration: '25 min',
    price: 'Incluido en Consulta',
    icon: 'scan',
    image:
      'https://images.unsplash.com/photo-1620916566398-39f1143ab7be?auto=format&fit=crop&w=600&q=80',
    desc: 'Evaluación computarizada científica de manchas profundas, poros, fotodaño y niveles de hidratación cutánea.',
    waText: 'Hola, quisiera agendar un diagnóstico facial digital FOCUSKIN en Salinas.',
  },
]

// Wellora Specialist Arched Team Data
const medicalTeam = [
  {
    name: 'Dra. Evelyn González',
    role: 'Directora Médica & Inyector Certificado',
    specialty: 'Medicina Estética & Armonización Facial',
    image:
      'https://images.unsplash.com/photo-1559839734-2b71ea197ec2?auto=format&fit=crop&w=800&q=85',
    alt: 'Dra. Evelyn González - Directora Médica DermoSTETICA',
    badge: '+10 Años de Trayectoria',
    link: defaultWhatsAppUrl,
  },
  {
    name: 'Lic. Marcela Viteri',
    role: 'Especialista en Láser & Fotónica',
    specialty: 'Harmony XL ClearLift & Remodelación',
    image:
      'https://images.unsplash.com/photo-1573496359142-b8d87734a5a2?auto=format&fit=crop&w=800&q=85',
    alt: 'Lic. Marcela Viteri - Especialista en Láser Harmony',
    badge: 'Tecnología Alma Lasers',
    link: defaultWhatsAppUrl,
  },
  {
    name: 'Dra. Camila Ramos',
    role: 'Cosmiatría & Nutrición Dérmica',
    specialty: 'EMFUSION & Hidratación Transdérmica',
    image:
      'https://images.unsplash.com/photo-1580489944761-15a19d654956?auto=format&fit=crop&w=800&q=85',
    alt: 'Dra. Camila Ramos - Cosmiatría y Cuidado Dérmico',
    badge: 'Revitalización Celular',
    link: defaultWhatsAppUrl,
  },
  {
    name: 'Lic. Romina Peña',
    role: 'Coordinadora de Armonización',
    specialty: 'Atención Exclusiva de Cabina & Post-Care',
    image:
      'https://images.unsplash.com/photo-1544005313-94ddf0286df2?auto=format&fit=crop&w=800&q=85',
    alt: 'Lic. Romina Peña - Coordinación y Bienestar de Pacientes',
    badge: 'Atención Privada Salinas',
    link: defaultWhatsAppUrl,
  },
]

// Infinite Slow-Moving Panoramic Ribbon Gallery (matching Image 1)
const slowGallery = [
  {
    image: 'https://images.unsplash.com/photo-1540555700478-4be289fbecef?auto=format&fit=crop&w=800&q=85',
    alt: 'Paciente relajada en cabina con flores y luz cálida',
    tag: 'Serenidad en Cabina',
  },
  {
    image: 'https://images.unsplash.com/photo-1544161515-4ab6ce6db874?auto=format&fit=crop&w=800&q=85',
    alt: 'Terapia estética relajante y bienestar corporal',
    tag: 'Bienestar & Calma',
  },
  {
    image: 'https://images.unsplash.com/photo-1512290923902-8a9f81dc236c?auto=format&fit=crop&w=800&q=85',
    alt: 'Rostro descansado y armonía facial en Salinas',
    tag: 'Armonización Facial',
  },
  {
    image: 'https://images.unsplash.com/photo-1515377905703-c4788e51af15?auto=format&fit=crop&w=800&q=85',
    alt: 'Piel luminosa, hidratada y tratada con criterio médico',
    tag: 'Luminosidad Dérmica',
  },
  {
    image: 'https://images.unsplash.com/photo-1570172619644-dfd03ed5d881?auto=format&fit=crop&w=800&q=85',
    alt: 'Tecnología médica en cabina DermoSTETICA Salinas',
    tag: 'Tecnología Harmony',
  },
  {
    image: 'https://images.unsplash.com/photo-1522337360788-8b13dee7a37e?auto=format&fit=crop&w=800&q=85',
    alt: 'Cuidado facial personalizado y protocolos no invasivos',
    tag: 'Protocolos Médicos',
  },
  {
    image: 'https://images.unsplash.com/photo-1519699047748-de8e457a634e?auto=format&fit=crop&w=800&q=85',
    alt: 'Experiencia relajante y segura con la Dra. Evelyn González',
    tag: 'Dra. Evelyn González',
  },
  {
    image: 'https://images.unsplash.com/photo-1629909613654-28e377c37b09?auto=format&fit=crop&w=800&q=85',
    alt: 'Instalaciones exclusivas en Salinas frente a Supermaxi',
    tag: 'Sede Salinas',
  },
]

let storyTimer: any = null

function openStory(id: number) {
  activeStoryModal.value = id
  startStoryTimer()
}

function startStoryTimer() {
  clearTimeout(storyTimer)
  storyTimer = setTimeout(() => {
    nextStory()
  }, 6500)
}

function closeStory() {
  activeStoryModal.value = null
  clearTimeout(storyTimer)
}

function nextStory() {
  if (activeStoryModal.value === null) return
  const currentIndex = stories.findIndex((s) => s.id === activeStoryModal.value)
  if (currentIndex < stories.length - 1) {
    activeStoryModal.value = stories[currentIndex + 1].id
    startStoryTimer()
  } else {
    closeStory()
  }
}

function prevStory() {
  if (activeStoryModal.value === null) return
  const currentIndex = stories.findIndex((s) => s.id === activeStoryModal.value)
  if (currentIndex > 0) {
    activeStoryModal.value = stories[currentIndex - 1].id
    startStoryTimer()
  }
}

function closeMenu() {
  menuOpen.value = false
}
</script>

<template>
  <div class="site-shell">
    <!-- Ambient iOS Glassmorphic Glow Orbs -->
    <div class="ambient-glow glow-1" aria-hidden="true"></div>
    <div class="ambient-glow glow-2" aria-hidden="true"></div>
    <div class="ambient-glow glow-3" aria-hidden="true"></div>

    <!-- Ambient Luminous Stardust & Light Refraction Layer -->
    <div class="luminous-dust-layer" aria-hidden="true">
      <span class="stardust dust-1">✦</span>
      <span class="stardust dust-2">✧</span>
      <span class="stardust dust-3">✦</span>
      <span class="stardust dust-4">✧</span>
      <span class="stardust dust-5">✦</span>
      <span class="stardust dust-6">✧</span>
    </div>

    <!-- Top Announcement Bar in Luxury Glassmorphism -->
    <div class="announcement">
      <div class="announcement-left">
        <span>MEDICINA ESTÉTICA Y LÁSER · +10 AÑOS DE EXPERIENCIA</span>
      </div>
      <div class="announcement-right">
        <span>Salinas, Ecuador · Lun-Vie 09H00-19H00 · Sáb 09H00-16H00</span>
        <span class="announcement-divider" aria-hidden="true">·</span>
        <a :href="instagramUrl" target="_blank" rel="noreferrer" class="announcement-link">
          <svg class="instagram-icon" viewBox="0 0 24 24" fill="currentColor">
            <path d="M12 2.163c3.204 0 3.584.012 4.85.07 3.252.148 4.771 1.691 4.919 4.919.058 1.265.069 1.645.069 4.849 0 3.205-.012 3.584-.069 4.849-.149 3.225-1.664 4.771-4.919 4.919-1.266.058-1.644.07-4.85.07-3.204 0-3.584-.012-4.849-.07-3.26-.149-4.771-1.699-4.919-4.92-.058-1.265-.07-1.644-.07-4.849 0-3.204.013-3.583.07-4.849.149-3.227 1.664-4.771 4.919-4.919 1.266-.057 1.645-.069 4.849-.069zm0-2.163c-3.259 0-3.667.014-4.947.072-4.358.2-6.78 2.618-6.98 6.98-.059 1.281-.073 1.689-.073 4.948 0 3.259.014 3.668.072 4.948.2 4.358 2.618 6.78 6.98 6.98 1.281.058 1.689.072 4.948.072 3.259 0 3.668-.014 4.948-.072 4.354-.2 6.782-2.618 6.979-6.98.059-1.28.073-1.689.073-4.948 0-3.259-.014-3.667-.072-4.947-.196-4.354-2.617-6.78-6.979-6.98-1.281-.059-1.69-.073-4.949-.073zm0 5.838c-3.403 0-6.162 2.759-6.162 6.162s2.759 6.163 6.162 6.163 6.162-2.759 6.162-6.163c0-3.403-2.759-6.162-6.162-6.162zm0 10.162c-2.209 0-4-1.79-4-4 0-2.209 1.791-4 4-4s4 1.791 4 4c0 2.21-1.791 4-4 4zm6.406-11.845c-.796 0-1.441.645-1.441 1.44s.645 1.44 1.441 1.44c.795 0 1.439-.645 1.439-1.44s-.644-1.44-1.439-1.44z"/>
          </svg>
          <span>@dermostetica_ec</span>
        </a>
      </div>
    </div>

    <!-- Official Floating Glass Capsule Header with Official DermoStetica Logo -->
    <header class="site-header" :class="{ 'is-scrolled': isScrolled }">
      <a class="brand-link" href="#inicio" aria-label="DermoStetica, volver al inicio" @click="closeMenu">
        <!-- Official DermoStetica Circular Emblem -->
        <div class="brand-emblem-official" aria-hidden="true">
          <svg viewBox="0 0 54 54" class="brand-emblem-svg" fill="none">
            <defs>
              <linearGradient id="navBrandGoldDisc" x1="0%" y1="0%" x2="100%" y2="100%">
                <stop offset="0%" stop-color="#FFFDF9" />
                <stop offset="100%" stop-color="#FEF5E3" />
              </linearGradient>
              <linearGradient id="navBrandGoldStem" x1="0%" y1="0%" x2="100%" y2="100%">
                <stop offset="0%" stop-color="#FDD87F" />
                <stop offset="38%" stop-color="#FA9E00" />
                <stop offset="72%" stop-color="#E59000" />
                <stop offset="100%" stop-color="#BD7400" />
              </linearGradient>
            </defs>
            <circle cx="27" cy="27" r="25.5" fill="url(#navBrandGoldDisc)" stroke="url(#navBrandGoldStem)" stroke-width="1.3" />
            <circle cx="27" cy="27" r="23.5" fill="none" stroke="url(#navBrandGoldStem)" stroke-width="0.5" stroke-opacity="0.35" />
            <!-- 3 Dots above D -->
            <circle cx="22.5" cy="11" r="1.1" fill="url(#navBrandGoldStem)" />
            <circle cx="27" cy="9.8" r="1.4" fill="url(#navBrandGoldStem)" />
            <circle cx="31.5" cy="11" r="1.1" fill="url(#navBrandGoldStem)" />
            <!-- Serif D stem & bowl -->
            <path
              d="M 16 14.5 h 8.5 c 7.2 0 12.2 4 12.2 12.2 c 0 8.2 -5 12.2 -12.2 12.2 h -8.5 z m 4.8 20.2 h 3.5 c 4.6 0 7.5 -2.4 7.5 -7.8 c 0 -5.4 -2.9 -7.8 -7.5 -7.8 h -3.5 z"
              fill="url(#navBrandGoldStem)"
            />
            <!-- Inner graceful face profile / floral loop curve -->
            <path
              d="M 24.5 25.5 C 26.5 22, 31.5 22, 32 26 C 32.5 30.5, 26.5 31.5, 23 34 C 27 35.8, 31 38.5, 27.5 41.5 C 24 43.5, 21 39.5, 24 35.5"
              fill="none"
              stroke="url(#navBrandGoldStem)"
              stroke-width="1.6"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
        </div>
        <div class="brand-text-official">
          <div class="brand-title-official">
            <span class="brand-dermo">Dermo</span><span class="brand-estetica">Stetica</span>
          </div>
          <span class="brand-tagline-official">MEDICINA ESTÉTICA Y LÁSER</span>
        </div>
      </a>

      <!-- Center Nav Links (Desktop) / Dropdown Drawer (Mobile/Tablet) -->
      <nav class="main-nav" :class="{ 'is-open': menuOpen }" aria-label="Navegación principal">
        <a href="#inicio" @click="closeMenu" class="nav-link-item">
          <span>Inicio</span>
          <svg viewBox="0 0 10 10" class="nav-chevron-svg" aria-hidden="true">
            <path d="M2.5 4l2.5 2.5 2.5-2.5" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
          </svg>
        </a>
        <a href="#filosofia" @click="closeMenu" class="nav-link-item">Nosotros</a>
        <!-- Celebratory 11th Anniversary Capsule Badge -->
        <a href="#aniversario" @click="closeMenu" class="nav-anniversary-badge" aria-label="Especial 11 Aniversario DermoStetica">
          <span class="nav-anniv-num-badge">11</span>
          <span class="nav-anniv-label">Aniversario</span>
          <span class="nav-anniv-sparkle" aria-hidden="true">✦</span>
        </a>
        <a href="#tratamientos" @click="closeMenu" class="nav-link-item">Servicios</a>
        <a href="#protocolos" @click="closeMenu" class="nav-link-item">Protocolos</a>
        <a href="#scanner" @click="closeMenu" class="nav-link-item">
          <span>Diagnóstico</span>
          <svg viewBox="0 0 10 10" class="nav-chevron-svg" aria-hidden="true">
            <path d="M2.5 4l2.5 2.5 2.5-2.5" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
          </svg>
        </a>
        <a href="#resultados" @click="closeMenu" class="nav-link-item">Resultados</a>
        <a href="#galeria" @click="closeMenu" class="nav-link-item">Galería</a>
        <a href="#clinica" @click="closeMenu" class="nav-link-item">Horarios</a>

        <!-- Revoza Deep Olive "Book Appointment" Pill CTA Button for Desktop -->
        <a class="revoza-appointment-btn revoza-desktop-btn" :href="defaultWhatsAppUrl" target="_blank" rel="noreferrer" @click="closeMenu">
          <span class="revoza-btn-circle" aria-hidden="true">
            <svg viewBox="0 0 16 16" class="revoza-arrow-icon" fill="none" stroke="currentColor">
              <path d="M4.5 11.5L11.5 4.5M11.5 4.5H6.5M11.5 4.5V9.5" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
          </span>
          <span class="revoza-btn-text">Agendar</span>
        </a>

        <!-- Drawer CTA Button inside open menu for Tablet/Mobile -->
        <a class="drawer-appointment-btn" :href="defaultWhatsAppUrl" target="_blank" rel="noreferrer" @click="closeMenu">
          <span class="revoza-btn-circle" aria-hidden="true">
            <svg viewBox="0 0 16 16" class="revoza-arrow-icon" fill="none" stroke="currentColor">
              <path d="M4.5 11.5L11.5 4.5M11.5 4.5H6.5M11.5 4.5V9.5" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
          </span>
          <span>Agendar en Salinas</span>
        </a>
      </nav>

      <!-- Responsive Header Actions (Agendar button + Hamburger menu toggle) -->
      <div class="header-actions">
        <a class="revoza-appointment-btn revoza-mobile-btn" :href="defaultWhatsAppUrl" target="_blank" rel="noreferrer" @click="closeMenu">
          <span class="revoza-btn-circle" aria-hidden="true">
            <svg viewBox="0 0 16 16" class="revoza-arrow-icon" fill="none" stroke="currentColor">
              <path d="M4.5 11.5L11.5 4.5M11.5 4.5H6.5M11.5 4.5V9.5" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
          </span>
          <span class="revoza-btn-text">Agendar</span>
        </a>

        <button
          class="menu-toggle"
          type="button"
          :aria-expanded="menuOpen"
          aria-label="Abrir menú de navegación"
          @click="menuOpen = !menuOpen"
        >
          <span></span>
          <span></span>
        </button>
      </div>
    </header>

    <main>
      <!-- Chea-Inspired Fullscreen Hero Slider -->
      <section
        id="inicio"
        class="chea-fullscreen-hero-slider"
        aria-label="Experiencia de bienvenida DermoSTETICA"
        @touchstart="handleSliderTouchStart"
        @touchend="handleSliderTouchEnd"
      >
        <!-- Background Slide Track with Ken Burns effect -->
        <div class="chea-slider-track">
          <div
            v-for="(slide, index) in heroSlides"
            :key="slide.id"
            class="chea-slide"
            :class="{
              'is-active': currentHeroSlide === index,
              'is-prev': (currentHeroSlide - 1 + heroSlides.length) % heroSlides.length === index,
            }"
            :aria-hidden="currentHeroSlide !== index"
          >
            <!-- Background Image with Ken Burns Scale -->
            <div
              class="chea-slide-bg"
              :style="{ backgroundImage: `url('${slide.bgImage}')` }"
              role="img"
              :aria-label="slide.bgAlt"
            ></div>
            <div class="chea-slide-overlay"></div>
            <div class="chea-slide-vignette"></div>

            <!-- Slide Content Canvas -->
            <div class="chea-slide-container section-wrap">
              <div class="chea-slide-inner">
                <!-- Tag / Eyebrow with gold star -->
                <div class="chea-slide-tag">
                  <span class="gold-sparkle" aria-hidden="true">✦</span>
                  <span>{{ slide.tag }}</span>
                </div>

                <!-- Grand Cormorant Garamond Heading -->
                <h1 class="chea-slide-title">
                  <span class="title-line">{{ slide.titleLine1 }}</span>
                  <em class="title-italic">{{ slide.titleItalic }}</em>
                  <span class="title-line">{{ slide.titleLine2 }}</span>
                </h1>

                <!-- Slide Description -->
                <p class="chea-slide-desc">{{ slide.description }}</p>

                <!-- Slide 1 Custom Credentials Pill Strip -->
                <div v-if="slide.credentials" class="chea-slide-credentials">
                  <div
                    v-for="(cred, cIdx) in slide.credentials"
                    :key="cIdx"
                    class="chea-cred-pill"
                  >
                    <span class="chea-cred-badge">{{ cred.badge }}</span>
                    <span class="chea-cred-text">{{ cred.text }}</span>
                  </div>
                </div>

                <!-- Slide 2 Chea 3-Pillar Feature Showcase Cards -->
                <div v-if="slide.features" class="chea-slide-features-grid">
                  <a
                    v-for="feat in slide.features"
                    :key="feat.title"
                    :href="feat.link"
                    class="chea-slide-feature-card chea-corner-frame"
                  >
                    <span class="chea-bracket-tr" aria-hidden="true"></span>
                    <span class="chea-bracket-bl" aria-hidden="true"></span>
                    <div class="feature-card-emblem">{{ feat.icon }}</div>
                    <strong class="feature-card-title">{{ feat.title }}</strong>
                    <span class="feature-card-sub">{{ feat.sub }}</span>
                    <p class="feature-card-desc">{{ feat.desc }}</p>
                    <span class="feature-card-link">
                      <span>Explorar</span>
                      <span class="link-arrow">↗</span>
                    </span>
                  </a>
                </div>

                <!-- Slide 3 Doctor Philosophy Card -->
                <div v-if="slide.doctorCard" class="chea-slide-doctor-card chea-corner-frame">
                  <span class="chea-bracket-tr" aria-hidden="true"></span>
                  <span class="chea-bracket-bl" aria-hidden="true"></span>
                  <p class="doctor-card-quote">{{ slide.doctorCard.quote }}</p>
                  <div class="doctor-card-footer">
                    <div class="doctor-card-author">
                      <strong>{{ slide.doctorCard.name }}</strong>
                      <span>{{ slide.doctorCard.role }}</span>
                      <small class="doctor-card-loc">📍 {{ slide.doctorCard.location }}</small>
                    </div>
                  </div>
                </div>

                <!-- Action Buttons with Chea Circled Button -->
                <div class="chea-slide-actions">
                  <a
                    class="chea-circle-action-btn"
                    :href="slide.primaryBtn.url"
                    :target="slide.primaryBtn.target"
                    rel="noreferrer"
                  >
                    <span class="circle-btn-label">{{ slide.primaryBtn.text }}</span>
                    <div class="circle-btn-ring-wrap" aria-hidden="true">
                      <svg class="circle-btn-svg" viewBox="0 0 44 44">
                        <circle cx="22" cy="22" r="20" class="ring-track" />
                        <circle cx="22" cy="22" r="20" class="ring-pulse" />
                      </svg>
                      <span class="circle-btn-icon">↗</span>
                    </div>
                  </a>

                  <a
                    class="button button-chea-outline"
                    :href="slide.secondaryBtn.url"
                    :target="slide.secondaryBtn.target"
                  >
                    <span>{{ slide.secondaryBtn.text }}</span>
                    <span aria-hidden="true">↓</span>
                  </a>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Chea Minimalist Navigation Controls -->
        <!-- Side Navigation Arrows -->
        <button
          class="chea-slider-arrow chea-arrow-prev"
          type="button"
          aria-label="Diapositiva anterior"
          @click="prevHeroSlide(true)"
        >
          <span class="arrow-symbol">‹</span>
        </button>

        <button
          class="chea-slider-arrow chea-arrow-next"
          type="button"
          aria-label="Siguiente diapositiva"
          @click="nextHeroSlide(true)"
        >
          <span class="arrow-symbol">›</span>
        </button>

        <!-- Bottom Controls Bar (Bullets + Scroll down) -->
        <div class="chea-slider-bottom-bar section-wrap">
          <!-- Circular Bullets with Animated SVG Progress Ring -->
          <div class="chea-slider-bullets" role="tablist" aria-label="Navegación de diapositivas">
            <button
              v-for="(slide, idx) in heroSlides"
              :key="slide.id"
              class="chea-bullet-btn"
              :class="{ 'is-active': currentHeroSlide === idx }"
              role="tab"
              :aria-selected="currentHeroSlide === idx"
              :aria-label="`Diapositiva ${idx + 1}`"
              @click="goToHeroSlide(idx)"
            >
              <span class="bullet-center-dot"></span>
              <svg class="bullet-svg-ring" viewBox="0 0 36 36" aria-hidden="true">
                <circle cx="18" cy="18" r="15" class="bullet-ring-bg" />
                <circle
                  cx="18"
                  cy="18"
                  r="15"
                  class="bullet-ring-progress"
                />
              </svg>
            </button>
          </div>

          <!-- Subtle Scroll Down Indicator -->
          <a href="#historias" class="chea-scroll-cue" aria-label="Desplazarse a historias">
            <span class="scroll-cue-text">EXPLORAR</span>
            <span class="scroll-cue-line" aria-hidden="true"></span>
          </a>
        </div>
      </section>

      <!-- Instagram Highlights Section (Interactive Stories Bar) -->
      <section id="historias" class="stories-section section-wrap" aria-label="Historias destacadas de Instagram">
        <div class="stories-header reveal-on-scroll">
          <div class="stories-title-group">
            <span class="stories-pill">
              <svg class="instagram-icon-small" viewBox="0 0 24 24" fill="currentColor">
                <path d="M12 2.163c3.204 0 3.584.012 4.85.07 3.252.148 4.771 1.691 4.919 4.919.058 1.265.069 1.645.069 4.849 0 3.205-.012 3.584-.069 4.849-.149 3.225-1.664 4.771-4.919 4.919-1.266.058-1.644.07-4.85.07-3.204 0-3.584-.012-4.849-.07-3.26-.149-4.771-1.699-4.919-4.92-.058-1.265-.07-1.644-.07-4.849 0-3.204.013-3.583.07-4.849.149-3.227 1.664-4.771 4.919-4.919 1.266-.057 1.645-.069 4.849-.069zm0-2.163c-3.259 0-3.667.014-4.947.072-4.358.2-6.78 2.618-6.98 6.98-.059 1.281-.073 1.689-.073 4.948 0 3.259.014 3.668.072 4.948.2 4.358 2.618 6.78 6.98 6.98 1.281.058 1.689.072 4.948.072 3.259 0 3.668-.014 4.948-.072 4.354-.2 6.782-2.618 6.979-6.98.059-1.28.073-1.689.073-4.948 0-3.259-.014-3.667-.072-4.947-.196-4.354-2.617-6.78-6.979-6.98-1.281-.059-1.69-.073-4.949-.073zm0 5.838c-3.403 0-6.162 2.759-6.162 6.162s2.759 6.163 6.162 6.163 6.162-2.759 6.162-6.163c0-3.403-2.759-6.162-6.162-6.162zm0 10.162c-2.209 0-4-1.79-4-4 0-2.209 1.791-4 4-4s4 1.791 4 4c0 2.21-1.791 4-4 4zm6.406-11.845c-.796 0-1.441.645-1.441 1.44s.645 1.44 1.441 1.44c.795 0 1.439-.645 1.439-1.44s-.644-1.44-1.439-1.44z"/>
              </svg>
              HISTORIAS DESTACADAS @DERMOSTETICA_EC
            </span>
            <h3>Explora lo más visto en nuestra comunidad</h3>
          </div>
          <a :href="instagramUrl" target="_blank" rel="noreferrer" class="stories-cta-link">
            Seguir en Instagram <span>↗</span>
          </a>
        </div>

        <div class="stories-grid reveal-stagger">
          <button
            v-for="story in stories"
            :key="story.id"
            class="story-circle-item"
            type="button"
            :aria-label="`Ver historia de ${story.title}`"
            @click="openStory(story.id)"
          >
            <div class="story-ring">
              <div class="story-inner-circle">
                <img
                  :src="story.coverImage"
                  :alt="story.title"
                  class="story-circle-photo"
                  loading="lazy"
                  decoding="async"
                />
              </div>
            </div>
            <span class="story-title">{{ story.title }}</span>
          </button>
        </div>
      </section>

      <!-- Chea-Inspired Minimalist Stat Counter Strip -->
      <section class="chea-counter-strip section-wrap" aria-label="Indicadores de excelencia clínica">
        <div class="chea-counter-grid reveal-stagger">
          <div v-for="(stat, index) in clinicStats" :key="stat.label" class="chea-counter-item">
            <div class="chea-counter-top">
              <span class="chea-counter-val">{{ stat.display }}</span>
            </div>
            <strong class="chea-counter-label">{{ stat.label }}</strong>
            <span class="chea-counter-sub">{{ stat.sub }}</span>
            <div v-if="index < clinicStats.length - 1" class="chea-counter-sep" aria-hidden="true"></div>
          </div>
        </div>
      </section>

      <!-- Brand Values Infinite Marquee Ribbon -->
      <section class="values-strip reveal-on-scroll" aria-label="Nuestra filosofía">
        <div class="marquee-track">
          <div class="marquee-content">
            <span>CIENCIA CON PROPÓSITO</span><i aria-hidden="true">✦</i>
            <span>BELLEZA EN EQUILIBRIO</span><i aria-hidden="true">✦</i>
            <span>ATENCIÓN MÉDICA CERCANA</span><i aria-hidden="true">✦</i>
            <span>RESULTADOS NATURALES</span><i aria-hidden="true">✦</i>
            <span>+10 AÑOS EN SALINAS</span><i aria-hidden="true">✦</i>
            <span>TECNOLOGÍA LÁSER HARMONY</span><i aria-hidden="true">✦</i>
          </div>
          <div class="marquee-content" aria-hidden="true">
            <span>CIENCIA CON PROPÓSITO</span><i aria-hidden="true">✦</i>
            <span>BELLEZA EN EQUILIBRIO</span><i aria-hidden="true">✦</i>
            <span>ATENCIÓN MÉDICA CERCANA</span><i aria-hidden="true">✦</i>
            <span>RESULTADOS NATURALES</span><i aria-hidden="true">✦</i>
            <span>+10 AÑOS EN SALINAS</span><i aria-hidden="true">✦</i>
            <span>TECNOLOGÍA LÁSER HARMONY</span><i aria-hidden="true">✦</i>
          </div>
        </div>
      </section>

      <!-- Wellora-Inspired 3 Oval Capsule Philosophy & Core Values (matching Image 1) -->
      <section id="filosofia" class="wellora-philosophy-section section-wrap">
        <div class="wellora-philosophy-header reveal-on-scroll">
          <div>
            <div class="eyebrow">
              <span class="gold-line"></span>
              <span>FILOSOFÍA DE ATENCIÓN &amp; VALORES CLÍNICOS</span>
            </div>
            <h2>Enfoque médico en tu<br /><em>bienestar y armonía natural.</em></h2>
          </div>
          <div class="philosophy-header-action">
            <a class="wellora-capsule-cta" :href="defaultWhatsAppUrl" target="_blank" rel="noreferrer">
              <span>Contactar Clínica</span>
              <span class="capsule-cta-circle" aria-hidden="true">↗</span>
            </a>
          </div>
        </div>

        <div class="wellora-capsules-grid reveal-stagger">
          <!-- Capsule 1: Our Mission -->
          <article class="wellora-oval-capsule">
            <div class="capsule-icon-wrap" aria-hidden="true">
              <svg viewBox="0 0 64 64" class="capsule-svg-icon" fill="none" stroke="currentColor">
                <path d="M32 16c6.6 0 12 5.4 12 12v6c0 6.6-5.4 12-12 12s-12-5.4-12-12v-6c0-6.6 5.4-12 12-12z" stroke-width="1.8" stroke-linecap="round"/>
                <path d="M20 28c-3 0-5.5 2-6 5l-2 9c-.5 2.5 1 5 3.5 5.5s5-1 5.5-3.5L22 38" stroke-width="1.6" stroke-linecap="round"/>
                <path d="M44 28c3 0 5.5 2 6 5l2 9c.5 2.5-1 5-3.5 5.5s-5-1-5.5-3.5L42 38" stroke-width="1.6" stroke-linecap="round"/>
                <path d="M26 46c2 2 4 3 6 3s4-1 6-3" stroke-width="1.6" stroke-linecap="round"/>
              </svg>
            </div>
            <h3 class="capsule-title">Nuestra Misión</h3>
            <p class="capsule-desc">
              Brindar una experiencia médica estética serena y segura, donde cada paciente reciba un diagnóstico anatómico individualizado enfocado en su armonía natural.
            </p>
            <div class="capsule-divider"></div>
            <div class="capsule-bullet">
              <span class="capsule-asterisk">✳</span>
              <span>Procedimientos 100% personalizados en cabina</span>
            </div>
          </article>

          <!-- Capsule 2: Our Vision -->
          <article class="wellora-oval-capsule">
            <div class="capsule-icon-wrap" aria-hidden="true">
              <svg viewBox="0 0 64 64" class="capsule-svg-icon" fill="none" stroke="currentColor">
                <path d="M32 20c-5-6-13-6-18 0-4.5 5.4-3.5 13.5 2 19l16 15 16-15c5.5-5.5 6.5-13.6 2-19-5-6-13-6-18 0z" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/>
                <circle cx="32" cy="27" r="3" stroke-width="1.6"/>
              </svg>
            </div>
            <h3 class="capsule-title">Nuestra Visión</h3>
            <p class="capsule-desc">
              Ser el santuario clínico de referencia en la Costa Ecuatoriana, integrando tecnología médica láser de vanguardia y tratamientos no invasivos de máxima excelencia.
            </p>
            <div class="capsule-divider"></div>
            <div class="capsule-bullet">
              <span class="capsule-asterisk">✳</span>
              <span>Tecnología láser Alma Harmony XL en Salinas</span>
            </div>
          </article>

          <!-- Capsule 3: Our Values -->
          <article class="wellora-oval-capsule">
            <div class="capsule-icon-wrap" aria-hidden="true">
              <svg viewBox="0 0 64 64" class="capsule-svg-icon" fill="none" stroke="currentColor">
                <circle cx="32" cy="32" r="20" stroke-width="1.8"/>
                <path d="M24 30c2-1 4-1 6 0M34 30c2-1 4-1 6 0" stroke-width="1.8" stroke-linecap="round"/>
                <path d="M26 38c3 3 9 3 12 0" stroke-width="1.8" stroke-linecap="round"/>
                <circle cx="32" cy="18" r="2" fill="currentColor"/>
              </svg>
            </div>
            <h3 class="capsule-title">Nuestros Valores</h3>
            <p class="capsule-desc">
              Priorizamos la ética médica, la honestidad en cada recomendación y productos certificados para cuidar tu salud antes que cualquier moda o sobrecorrección.
            </p>
            <div class="capsule-divider"></div>
            <div class="capsule-bullet">
              <span class="capsule-asterisk">✳</span>
              <span>Compromiso irrenunciable con la naturalidad</span>
            </div>
          </article>
        </div>

        <!-- Wellora Trust Badge Strip (matching Image 1 bottom) -->
        <div class="wellora-trust-bar reveal-on-scroll">
          <div class="trust-rating-box">
            <span class="trust-rating-label">Avalado por <strong>+2,500 Pacientes Satisfechos</strong></span>
            <div class="trust-stars" aria-label="Calificación 4.9 de 5 estrellas">
              <span>★</span><span>★</span><span>★</span><span>★</span><span>★</span>
              <strong class="trust-score">4.9 / 5</strong>
            </div>
          </div>
          <div class="trust-contact-pill">
            <div class="trust-avatar-duo">
              <img src="https://images.unsplash.com/photo-1559839734-2b71ea197ec2?auto=format&fit=crop&w=120&q=80" alt="Dra. Evelyn González" class="trust-avatar-img" loading="lazy" decoding="async"/>
              <div class="trust-phone-badge" aria-hidden="true">
                <svg viewBox="0 0 24 24" class="trust-phone-svg" fill="none" stroke="currentColor">
                  <path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z" stroke-width="1.8"/>
                </svg>
              </div>
            </div>
            <p class="trust-invite">
              ¿Deseas una valoración profesional para tu rostro?
              <a :href="defaultWhatsAppUrl" target="_blank" rel="noreferrer" class="trust-invite-link">Agendar en WhatsApp ↗</a>
            </p>
          </div>
        </div>
      </section>

      <!-- Digital Facial Architecture & Interactive Scanner Section (Redesigned matching Image 1) -->
      <section id="scanner" class="wellora-feature-section section-wrap">
        <div class="wellora-feature-header reveal-on-scroll">
          <div class="eyebrow">
            <span class="gold-line"></span>
            <span>MAPA ANATÓMICO &amp; TECNOLOGÍA CLÍNICA</span>
          </div>
          <h2>Diagnóstico Facial Digital &amp; Zonas de <em>Armonización.</em></h2>
          <p>
            Cada área del rostro posee una densidad dérmica y mímica muscular única.
            Selecciona cualquier punto anatómico para descubrir el protocolo médico recomendado por la Dra. Evelyn González.
          </p>
        </div>

        <div class="wellora-feature-layout reveal-card">
          <!-- Left Column: Overlapping Dual Photos + Botanical Line Art + Rotating Stamp Badge -->
          <div class="wellora-feature-visual">
            <!-- Delicate Botanical Foliage Line Art SVG (matching Image 1) -->
            <svg class="wellora-foliage-sketch" viewBox="0 0 160 160" fill="none" stroke="#7E8D79" stroke-width="1.3" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
              <path d="M 25,145 Q 60,95 95,45 Q 115,20 135,12" />
              <path d="M 68,86 C 58,78 44,79 40,92 C 50,97 63,94 68,86 Z" />
              <path d="M 82,62 C 92,54 105,57 109,70 C 99,74 86,71 82,62 Z" />
              <path d="M 98,38 C 88,30 74,32 70,45 C 81,49 94,47 98,38 Z" />
              <path d="M 112,24 C 122,16 135,18 139,31 C 129,35 116,33 112,24 Z" />
            </svg>

            <!-- Primary Photo Card: Interactive Clinical Facial Architecture Canvas -->
            <div class="wellora-photo-primary scanner-photo-primary">
              <img
                src="https://images.unsplash.com/photo-1596755389378-c31d21fd1273?auto=format&fit=crop&w=1200&q=85"
                alt="Mapa anatómico facial DermoSTETICA"
                class="wellora-primary-img"
                fetchpriority="high"
                decoding="async"
              />

              <!-- Laser Scan Beam Sweep -->
              <div class="laser-scan-beam" aria-hidden="true">
                <div class="laser-beam-line"></div>
                <div class="laser-beam-aura"></div>
              </div>

              <!-- Coordinate Grid Overlay -->
              <div class="scanner-grid-overlay" aria-hidden="true"></div>

              <!-- Interactive Clinical Hotspots -->
              <button
                v-for="(zone, index) in facialZones"
                :key="zone.id"
                class="scanner-hotspot"
                :class="{ 'is-active': selectedFacialZone === index }"
                :style="{ left: `${zone.coords.x}%`, top: `${zone.coords.y}%` }"
                type="button"
                :aria-label="`Zona anatómica: ${zone.name}`"
                @click="selectedFacialZone = index"
                @mouseenter="selectedFacialZone = index"
              >
                <span class="hotspot-pulse"></span>
                <span class="hotspot-dot"></span>
                <span class="hotspot-tag">{{ zone.shortName }}</span>
              </button>

              <div class="scanner-status-chip">
                <span class="status-live-dot"></span>
                <span>ESCÁNER CLÍNICO ACTIVO · SALINAS</span>
              </div>
            </div>

            <!-- Secondary Overlapping Photo Card (Warm Cabina Spa Treatment, matching Image 1) -->
            <div class="wellora-photo-secondary">
              <img
                src="https://images.unsplash.com/photo-1570172619644-dfd03ed5d881?auto=format&fit=crop&w=800&q=85"
                alt="Tratamiento en cabina médica DermoSTETICA"
                class="wellora-secondary-img"
                loading="lazy"
                decoding="async"
              />
            </div>

            <!-- Rotating Luxury Circular Stamp Badge (matching Image 1) -->
            <div class="wellora-stamp-badge" aria-hidden="true">
              <svg class="stamp-rotating-svg" viewBox="0 0 140 140">
                <path id="stampScannerPath" d="M 70, 70 m -50, 0 a 50,50 0 1,1 100,0 a 50,50 0 1,1 -100,0" fill="none" />
                <text class="stamp-circular-text">
                  <textPath href="#stampScannerPath" startOffset="0%">
                    ★ DIAGNÓSTICO MÉDICO ★ DERMOSTETICA SALINAS
                  </textPath>
                </text>
              </svg>
              <div class="stamp-center-icon">
                <svg viewBox="0 0 24 24" class="stamp-leaf-svg" fill="currentColor">
                  <path d="M12 2C8 6 6 10 6 14a6 6 0 0 0 12 0c0-4-2-8-6-12zm0 15a3 3 0 0 1-3-3c0-2.5 1.5-5 3-7 1.5 2 3 4.5 3 7a3 3 0 0 1-3 3z"/>
                </svg>
              </div>
            </div>
          </div>

          <!-- Right Column: Soft Sage/Linen Card with Numbered Accordion List (matching Image 1) -->
          <div class="wellora-feature-accordion-card">
            <div class="wellora-accordion-list">
              <div
                v-for="(zone, index) in facialZones"
                :key="zone.id"
                class="wellora-accordion-item"
                :class="{ 'is-expanded': selectedFacialZone === index }"
              >
                <button
                  class="wellora-accordion-trigger"
                  type="button"
                  @click="selectedFacialZone = (selectedFacialZone === index ? -1 : index)"
                >
                  <span class="wellora-item-number-title">
                    <strong class="item-index">0{{ index + 1 }}.</strong>
                    <span class="item-label">{{ zone.name }}</span>
                  </span>
                  <span class="wellora-chevron-circle" :class="{ 'is-open': selectedFacialZone === index }" aria-hidden="true">
                    <svg viewBox="0 0 20 20" class="chevron-svg" fill="none" stroke="currentColor">
                      <path d="M5 8l5 5 5-5" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                    </svg>
                  </span>
                </button>

                <!-- Collapsible Content (matching Image 1: description + floral asterisk bullets + cta) -->
                <div v-show="selectedFacialZone === index" class="wellora-accordion-body">
                  <p class="accordion-body-desc">{{ zone.mechanism }}</p>
                  <ul class="accordion-bullets-list">
                    <li>
                      <span class="bullet-asterisk">✻</span>
                      <div>
                        <strong>Protocolo Médico:</strong> {{ zone.treatment }}
                      </div>
                    </li>
                    <li>
                      <span class="bullet-asterisk">✻</span>
                      <div>
                        <strong>Indicación Principal:</strong> {{ zone.indication }}
                      </div>
                    </li>
                    <li>
                      <span class="bullet-asterisk">✻</span>
                      <div>
                        <strong>Resultados Esperados:</strong> {{ zone.results }}
                      </div>
                    </li>
                    <li>
                      <span class="bullet-asterisk">✻</span>
                      <div>
                        <strong>Cabina &amp; Recuperación:</strong> {{ zone.duration }} · {{ zone.downtime }}
                      </div>
                    </li>
                  </ul>

                  <div class="accordion-action-row">
                    <a
                      class="wellora-accordion-cta"
                      :href="getWhatsAppUrl(zone.waText)"
                      target="_blank"
                      rel="noreferrer"
                    >
                      <span>Agendar valoración para esta zona</span>
                      <span class="cta-circle-arrow" aria-hidden="true">↗</span>
                    </a>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Official 11th Anniversary Promotions Showcase Section with Special Commemorative Frame -->
      <section id="aniversario" class="anniversary-section section-wrap">
        <div class="anniversary-grand-frame reveal-card">
          <!-- Festive Commemorative Frame Crown Plaque -->
          <div class="anniv-frame-crown" aria-hidden="true">
            <span class="crown-sparkle">✦</span>
            <span>EDICIÓN ESPECIAL · 11.º ANIVERSARIO</span>
            <span class="crown-sparkle">✦</span>
          </div>

          <!-- Delicate Golden Corner Stars -->
          <span class="anniv-corner-star corner-tl" aria-hidden="true">✦</span>
          <span class="anniv-corner-star corner-tr" aria-hidden="true">✦</span>
          <span class="anniv-corner-star corner-bl" aria-hidden="true">✦</span>
          <span class="anniv-corner-star corner-br" aria-hidden="true">✦</span>

          <!-- Botanical Foliage SVG Accent -->
          <div class="wellora-foliage-accent anniversary-foliage" aria-hidden="true">
            <svg viewBox="0 0 160 120" fill="none" class="foliage-svg">
              <path d="M10 110 C 30 70, 70 30, 150 10" stroke="#FA9E00" stroke-width="1.2" stroke-linecap="round" opacity="0.35" />
              <path d="M35 85 C 45 75, 60 78, 65 90 C 55 95, 42 92, 35 85 Z" fill="#FFE8BF" stroke="#FA9E00" stroke-width="0.8" opacity="0.5" />
              <path d="M70 55 C 82 45, 98 48, 102 62 C 90 68, 76 63, 70 55 Z" fill="#FFE8BF" stroke="#FA9E00" stroke-width="0.8" opacity="0.5" />
              <path d="M110 30 C 122 18, 140 22, 142 36 C 130 40, 116 36, 110 30 Z" fill="#FFE8BF" stroke="#FA9E00" stroke-width="0.8" opacity="0.5" />
              <path d="M50 70 C 45 55, 55 42, 68 45 C 70 58, 60 68, 50 70 Z" fill="#FFE8BF" stroke="#FA9E00" stroke-width="0.8" opacity="0.4" />
            </svg>
          </div>

          <div class="anniversary-header reveal-on-scroll">
            <div class="anniversary-header-left">
              <div class="eyebrow">
                <span class="gold-line"></span>
                <span>11.º ANIVERSARIO · DERMOSTETICA SALINAS</span>
              </div>
              <h2>Celebramos 11 Años de Cuidado Médico<br />con <em>Tarifas y Regalos de Aniversario.</em></h2>
              <p>
                11 años cuidando de ti en Salinas con excelencia y criterio médico. Aprovecha estos paquetes
                conmemorativos por tiempo limitado con diagnósticos FOCUSKIN computarizados y atenciones exclusivas de cortesía.
              </p>
            </div>

            <!-- Rotating Circular Anniversary Stamp Badge with Golden Glow Contour -->
            <div class="anniversary-stamp" aria-hidden="true">
              <div class="stamp-svg-wrap">
                <svg viewBox="0 0 140 140" class="rotating-text-svg">
                  <path
                    id="annivStampCircle"
                    d="M 70, 70 m -50, 0 a 50,50 0 1,1 100,0 a 50,50 0 1,1 -100,0"
                    fill="none"
                  />
                  <text class="stamp-text">
                    <textPath href="#annivStampCircle" startOffset="0%">
                      ✦ 11 ANIVERSARIO · DERMOSTETICA ✦ SALINAS ✦
                    </textPath>
                  </text>
                </svg>
                <div class="stamp-center-emblem">
                  <span class="anniv-stamp-number">11</span>
                </div>
              </div>
            </div>
          </div>

          <!-- Filter Tabs for Anniversary Treatments -->
          <div class="anniversary-filters reveal-on-scroll">
            <div class="filter-glass-track">
              <button
                type="button"
                class="filter-pill"
                :class="{ active: selectedAnniversaryCategory === 'all' }"
                @click="selectedAnniversaryCategory = 'all'"
              >
                Todos ({{ anniversaryPackages.length }})
              </button>
              <button
                type="button"
                class="filter-pill"
                :class="{ active: selectedAnniversaryCategory === 'regen' }"
                @click="selectedAnniversaryCategory = 'regen'"
              >
                Glow &amp; Nanopore
              </button>
              <button
                type="button"
                class="filter-pill"
                :class="{ active: selectedAnniversaryCategory === 'lifting' }"
                @click="selectedAnniversaryCategory = 'lifting'"
              >
                Lifting &amp; Láser NIR
              </button>
              <button
                type="button"
                class="filter-pill"
                :class="{ active: selectedAnniversaryCategory === 'skin' }"
                @click="selectedAnniversaryCategory = 'skin'"
              >
                Hidratación &amp; Spa
              </button>
            </div>
          </div>

          <!-- Luxury Cards Grid Redesigned matching Image 1 -->
          <div class="anniversary-grid reveal-stagger">
            <article
              v-for="(pkg, idx) in filteredAnniversaryPackages"
              :key="pkg.id"
              class="anniversary-lux-card"
            >
              <!-- Inset Photographic Media Frame with Commemorative Frame -->
              <div class="anniversary-card-media">
                <img
                  :src="pkg.image"
                  :alt="pkg.alt"
                  class="anniversary-card-img"
                  loading="lazy"
                  decoding="async"
                />
                <div class="anniversary-media-top">
                  <span class="anniversary-media-sessions">{{ pkg.sessions }}</span>
                  <div class="anniversary-media-pricing">
                    <span class="anniv-price-current">{{ pkg.price }}</span>
                    <span v-if="pkg.originalPrice" class="anniv-price-orig">{{ pkg.originalPrice }}</span>
                  </div>
                </div>
              </div>

              <!-- Card Body Content -->
              <div class="anniversary-card-content">
                <span class="anniversary-card-eyebrow">✦ EDICIÓN 11.º ANIVERSARIO · {{ pkg.techTag }}</span>
                <h3 class="anniversary-card-title">
                  <strong class="item-index">0{{ idx + 1 }}.</strong>
                  <span class="item-label">{{ pkg.name }}</span>
                </h3>
                <p class="anniversary-card-subtitle">{{ pkg.subtitle }}</p>

                <!-- Medical Bullets List -->
                <ul class="accordion-bullets-list anniversary-bullets-list">
                  <li>
                    <span class="bullet-asterisk" aria-hidden="true">✻</span>
                    <div>
                      <strong>Protocolo Clínico:</strong> {{ pkg.protocol }}
                    </div>
                  </li>
                  <li>
                    <span class="bullet-asterisk" aria-hidden="true">✻</span>
                    <div>
                      <strong>Beneficio Médico:</strong> {{ pkg.benefit }}
                    </div>
                  </li>
                  <li>
                    <span class="bullet-asterisk" aria-hidden="true">✻</span>
                    <div>
                      <strong>Tiempo &amp; Recuperación:</strong> {{ pkg.duration }} · {{ pkg.downtime }}
                    </div>
                  </li>
                </ul>

                <!-- Elegant Quote-Style Gift / Inclusion Box -->
                <blockquote class="wellora-accordion-quote anniversary-quote-box">
                  <p>«{{ pkg.gift || pkg.included || pkg.tag }}»</p>
                  <small v-if="pkg.gift">🎁 Obsequio de Aniversario · Cortesía</small>
                  <small v-else-if="pkg.included">✨ Protocolo Incluido · Especial 11 Años</small>
                  <small v-else>★ Tarifa Conmemorativa · 11.º Aniversario</small>
                </blockquote>
              </div>

              <!-- Card Action Footer with Sleek Dark Noir Pill CTA -->
              <div class="anniversary-card-footer">
                <a
                  class="wellora-accordion-cta anniversary-dark-btn"
                  :href="getWhatsAppUrl(pkg.waText, whatsappNumber2)"
                  target="_blank"
                  rel="noreferrer"
                >
                  <span>Reservar este procedimiento</span>
                  <span class="cta-arrow" aria-hidden="true">↗</span>
                </a>
              </div>
            </article>
          </div>

          <!-- Bottom Notice -->
          <div class="anniversary-footer-notice reveal-on-scroll">
            <span>✦ Tarifas conmemorativas exclusivas por el mes de Aniversario en Salinas · Cupos limitados bajo agenda previa ✦</span>
          </div>
        </div>
      </section>

      <!-- Services Section with Gold Accents & Filter Tabs -->
      <section id="tratamientos" class="services-section section-wrap">
        <div class="section-heading reveal-on-scroll">
          <div>
            <div class="eyebrow">
              <span class="gold-line"></span>
              <span>NUESTROS TRATAMIENTOS</span>
            </div>
            <h2>Un cuidado médico<br />pensado para <em>ti.</em></h2>
          </div>
          <div class="heading-aside">
            <p>
              La Dra. Evelyn González diseña cada protocolo según tus características anatómicas.
              Juntas definimos los pasos ideales para tu bienestar.
            </p>
            <a class="text-link" :href="bookingUrl" target="_blank" rel="noreferrer">
              Ver catálogo completo en Taplink <span>↗</span>
            </a>
          </div>
        </div>

        <!-- Filter category tabs with iOS Segmented Glass Track -->
        <div class="service-filters reveal-on-scroll" role="tablist">
          <div class="filter-glass-track">
            <button
              class="filter-pill"
              :class="{ active: selectedCategory === 'all' }"
              type="button"
              @click="selectedCategory = 'all'"
            >
              Todos ({{ services.length }})
            </button>
            <button
              class="filter-pill"
              :class="{ active: selectedCategory === 'injectables' }"
              type="button"
              @click="selectedCategory = 'injectables'"
            >
              Inyectables &amp; Armonización
            </button>
            <button
              class="filter-pill"
              :class="{ active: selectedCategory === 'laser' }"
              type="button"
              @click="selectedCategory = 'laser'"
            >
              Láser Harmony XL
            </button>
            <button
              class="filter-pill"
              :class="{ active: selectedCategory === 'skin' }"
              type="button"
              @click="selectedCategory = 'skin'"
            >
              Revitalización &amp; Piel
            </button>
          </div>
        </div>

        <!-- Service Cards Grid -->
        <div class="service-grid luxury-service-grid reveal-stagger">
          <article v-for="service in filteredServices" :key="service.name" class="service-card luxury-card">
            <div class="service-image-link luxury-image-frame">
              <img :src="service.image" :alt="service.alt" loading="lazy" decoding="async" />
              <div class="service-card-overlay"></div>
              <div class="service-card-top-row">
                <span class="service-badge">{{ service.badge }}</span>
                <span class="service-card-glow-dot" aria-hidden="true">✦</span>
              </div>
              <a
                class="service-wa-badge luxury-consult-pill"
                :href="getWhatsAppUrl(service.waText)"
                target="_blank"
                rel="noreferrer"
                :aria-label="`Consultar ${service.name} por WhatsApp`"
              >
                <span>Consultar</span>
                <span class="pill-arrow" aria-hidden="true">↗</span>
              </a>
            </div>

            <div class="service-info">
              <div class="service-category-tag">
                <span class="tag-sparkle" aria-hidden="true">✧</span>
                <span>{{ service.subtitle }}</span>
              </div>
              <h3 class="service-card-title">{{ service.name }}</h3>
              <p class="service-card-desc">{{ service.description }}</p>
              <div class="service-meta luxury-meta-strip">
                <div class="meta-pill">
                  <span class="meta-icon">⏱</span>
                  <span>{{ service.duration }}</span>
                </div>
                <div class="meta-pill">
                  <span class="meta-icon">✨</span>
                  <span>{{ service.downtime }}</span>
                </div>
              </div>
            </div>
          </article>
        </div>

        <p class="service-disclaimer">
          * La indicación médica y los resultados de cada procedimiento dependen de una valoración profesional individualizada en nuestra clínica de Salinas.
        </p>
      </section>

      <!-- Wellora 2-Column Editorial Treatment Session Menu with Interactive Floating Preview -->
      <section id="protocolos" class="wellora-menu-section section-wrap">
        <!-- Delicate Botanical Foliage SVG Accent (matching Image 3) -->
        <div class="wellora-foliage-accent" aria-hidden="true">
          <svg viewBox="0 0 160 120" fill="none" class="foliage-svg">
            <path d="M10 110 C 30 70, 70 30, 150 10" stroke="#C5A059" stroke-width="1.2" stroke-linecap="round" opacity="0.3" />
            <path d="M35 85 C 45 75, 60 78, 65 90 C 55 95, 42 92, 35 85 Z" fill="#EAD9BC" stroke="#C5A059" stroke-width="0.8" opacity="0.45" />
            <path d="M70 55 C 82 45, 98 48, 102 62 C 90 68, 76 63, 70 55 Z" fill="#EAD9BC" stroke="#C5A059" stroke-width="0.8" opacity="0.45" />
            <path d="M110 30 C 122 18, 140 22, 142 36 C 130 40, 116 36, 110 30 Z" fill="#EAD9BC" stroke="#C5A059" stroke-width="0.8" opacity="0.45" />
            <path d="M50 70 C 45 55, 55 42, 68 45 C 70 58, 60 68, 50 70 Z" fill="#EAD9BC" stroke="#C5A059" stroke-width="0.8" opacity="0.35" />
          </svg>
        </div>

        <div class="section-heading reveal-on-scroll">
          <div>
            <div class="eyebrow">
              <span class="gold-line"></span>
              <span>CARTA DE PROTOCOLOS MÉDICOS EN CABINA</span>
            </div>
            <h2>Experiencias de Alta Precisión &amp;<br /><em>Serenidad</em> en Salinas.</h2>
          </div>
          <div class="heading-aside">
            <p>
              Explora nuestros protocolos combinados. Pasa el cursor o pulsa el icono <strong class="gold-highlight">⊙</strong> de cualquier tratamiento para ver su vista previa visual en cabina.
            </p>
          </div>
        </div>

        <!-- 2-Column Wellora Menu Grid -->
        <div class="wellora-menu-card reveal-card">
          <div class="wellora-menu-grid">
            <article
              v-for="(item, index) in editorialSessions"
              :key="item.title"
              class="wellora-item"
              :class="{ 'is-active': hoveredProtocol === index }"
              @mouseenter="hoveredProtocol = index"
              @mouseleave="hoveredProtocol = null"
            >
              <div class="wellora-item-top">
                <!-- Line-Art Medical Icon Column -->
                <div class="wellora-icon-box" aria-hidden="true">
                  <!-- Syringe Icon -->
                  <svg v-if="item.icon === 'syringe'" viewBox="0 0 24 24" class="wellora-line-icon" fill="none" stroke="currentColor">
                    <line x1="18" y1="2" x2="22" y2="6" stroke-width="1.5" />
                    <line x1="17" y1="7" x2="7" y2="17" stroke-width="1.5" />
                    <line x1="14" y1="4" x2="20" y2="10" stroke-width="1.5" />
                    <polyline points="5 15 2 18 6 22 9 19" stroke-width="1.5" />
                    <line x1="2" y1="22" x2="5" y2="19" stroke-width="1.5" />
                  </svg>
                  <!-- Lips Icon -->
                  <svg v-else-if="item.icon === 'lips'" viewBox="0 0 24 24" class="wellora-line-icon" fill="none" stroke="currentColor">
                    <path d="M3 13 C 6 8, 10 9, 12 11 C 14 9, 18 8, 21 13 C 18 19, 14 20, 12 16 C 10 20, 6 19, 3 13 Z" stroke-width="1.5" stroke-linejoin="round" />
                    <path d="M3 13 C 8 15, 16 15, 21 13" stroke-width="1.4" />
                  </svg>
                  <!-- Profile / Nose Icon -->
                  <svg v-else-if="item.icon === 'profile'" viewBox="0 0 24 24" class="wellora-line-icon" fill="none" stroke="currentColor">
                    <path d="M16 3 C 13 4, 11 7, 11 10 C 11 12, 9 13, 8 14 L 6 16 H 11 C 14 16, 16 14, 16 11 Z" stroke-width="1.5" stroke-linecap="round" />
                    <circle cx="10" cy="18" r="1" fill="currentColor" />
                  </svg>
                  <!-- Laser Icon -->
                  <svg v-else-if="item.icon === 'laser'" viewBox="0 0 24 24" class="wellora-line-icon" fill="none" stroke="currentColor">
                    <circle cx="12" cy="12" r="3" stroke-width="1.5" />
                    <line x1="12" y1="2" x2="12" y2="5" stroke-width="1.5" />
                    <line x1="12" y1="19" x2="12" y2="22" stroke-width="1.5" />
                    <line x1="2" y1="12" x2="5" y2="12" stroke-width="1.5" />
                    <line x1="19" y1="12" x2="22" y2="12" stroke-width="1.5" />
                    <line x1="5" y1="5" x2="7" y2="7" stroke-width="1.5" />
                    <line x1="17" y1="17" x2="19" y2="19" stroke-width="1.5" />
                  </svg>
                  <!-- Contour Icon -->
                  <svg v-else-if="item.icon === 'contour'" viewBox="0 0 24 24" class="wellora-line-icon" fill="none" stroke="currentColor">
                    <path d="M4 6 C 8 16, 16 20, 20 20" stroke-width="1.5" stroke-linecap="round" />
                    <path d="M7 3 C 12 11, 17 14, 21 15" stroke-width="1.2" stroke-dasharray="2 2" />
                    <circle cx="12" cy="12" r="2" fill="currentColor" />
                  </svg>
                  <!-- Droplet Icon -->
                  <svg v-else-if="item.icon === 'droplet'" viewBox="0 0 24 24" class="wellora-line-icon" fill="none" stroke="currentColor">
                    <path d="M12 2.69 L 6 12 A 6 6 0 0 0 18 12 L 12 2.69 Z" stroke-width="1.5" stroke-linejoin="round" />
                  </svg>
                  <!-- Sparkle Icon -->
                  <svg v-else-if="item.icon === 'sparkle'" viewBox="0 0 24 24" class="wellora-line-icon" fill="none" stroke="currentColor">
                    <path d="M12 2 L 14.5 9.5 L 22 12 L 14.5 14.5 L 12 22 L 9.5 14.5 L 2 12 L 9.5 9.5 Z" stroke-width="1.5" stroke-linejoin="round" />
                  </svg>
                  <!-- Scan / Focuskin Icon -->
                  <svg v-else viewBox="0 0 24 24" class="wellora-line-icon" fill="none" stroke="currentColor">
                    <path d="M3 7 V 5 A 2 2 0 0 1 5 3 H 7" stroke-width="1.5" stroke-linecap="round" />
                    <path d="M17 3 H 19 A 2 2 0 0 1 21 5 V 7" stroke-width="1.5" stroke-linecap="round" />
                    <path d="M21 17 V 19 A 2 2 0 0 1 19 21 H 17" stroke-width="1.5" stroke-linecap="round" />
                    <path d="M7 21 H 5 A 2 2 0 0 1 3 19 V 17" stroke-width="1.5" stroke-linecap="round" />
                    <circle cx="12" cy="12" r="3" stroke-width="1.5" />
                  </svg>
                </div>

                <!-- Title & Interactive Preview Indicator Button -->
                <div class="wellora-title-wrap">
                  <h3 class="wellora-title">{{ item.title }}</h3>
                  
                  <!-- Interactive Hover Indicator Button ⊙ matching Image 3 -->
                  <button
                    class="wellora-thumb-trigger"
                    type="button"
                    :aria-label="`Ver vista previa de ${item.title}`"
                    @click.stop="hoveredProtocol = hoveredProtocol === index ? null : index"
                  >
                    <span class="trigger-circle"></span>
                    <span class="trigger-dot"></span>

                    <!-- Floating Oval Image Preview blooming on hover (Image 3) -->
                    <div
                      class="floating-thumb-preview"
                      :class="{ 'is-visible': hoveredProtocol === index }"
                      aria-hidden="true"
                    >
                      <div class="floating-thumb-inner">
                        <img :src="item.image" :alt="item.title" loading="lazy" decoding="async" />
                        <span class="floating-thumb-badge">{{ item.duration }}</span>
                      </div>
                    </div>
                  </button>
                </div>

                <!-- Dotted Leader Line (matching Image 3) -->
                <div class="wellora-dotted-line" aria-hidden="true"></div>

                <!-- Duration & Price / Category Badge -->
                <div class="wellora-meta-col">
                  <span class="wellora-price">{{ item.price }}</span>
                  <span class="wellora-duration">{{ item.duration }}</span>
                </div>
              </div>

              <!-- Description and WhatsApp Quick Action -->
              <div class="wellora-item-sub">
                <p class="wellora-desc">{{ item.desc }}</p>
                <a
                  class="wellora-action-link"
                  :href="getWhatsAppUrl(item.waText)"
                  target="_blank"
                  rel="noreferrer"
                  :aria-label="`Consultar ${item.title} en WhatsApp`"
                >
                  <span>Consultar</span>
                  <span class="action-arrow">↗</span>
                </a>
              </div>
            </article>
          </div>

          <!-- Bottom Cabina Philosophy Bar -->
          <div class="wellora-footer-banner">
            <div class="banner-quote">
              <span class="quote-star">✦</span>
              <p>«Cada protocolo médico está diseñado para respetar tu fisionomía y brindar resultados visibles con confort absoluto.»</p>
            </div>
            <a
              class="banner-cta-button"
              :href="defaultWhatsAppUrl"
              target="_blank"
              rel="noreferrer"
            >
              <span>Agendar Valoración Médica en Salinas</span>
              <span aria-hidden="true">↗</span>
            </a>
          </div>
        </div>
      </section>

      <!-- Interactive Aesthetic Results Showcase (Redesigned matching Image 1) -->
      <section id="resultados" class="wellora-feature-section results-feature-section section-wrap">
        <div class="wellora-feature-header reveal-on-scroll">
          <div class="eyebrow">
            <span class="gold-line"></span>
            <span>ARMONIZACIÓN &amp; RESULTADOS REALES</span>
          </div>
          <h2>El arte de realzar tu belleza <em>natural.</em></h2>
          <p>
            Explora casos clínicos reales de la Dra. Evelyn González. Desliza la barra interactiva para comparar el cambio antes y después, y conoce cada detalle del protocolo médico.
          </p>
        </div>

        <div class="wellora-feature-layout reveal-card">
          <!-- Left Column: Interactive Before/After Split Slider + Botanical Line Art + Rotating Stamp Badge -->
          <div class="wellora-feature-visual">
            <!-- Delicate Botanical Foliage Line Art SVG (matching Image 1) -->
            <svg class="wellora-foliage-sketch" viewBox="0 0 160 160" fill="none" stroke="#7E8D79" stroke-width="1.3" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
              <path d="M 25,145 Q 60,95 95,45 Q 115,20 135,12" />
              <path d="M 68,86 C 58,78 44,79 40,92 C 50,97 63,94 68,86 Z" />
              <path d="M 82,62 C 92,54 105,57 109,70 C 99,74 86,71 82,62 Z" />
              <path d="M 98,38 C 88,30 74,32 70,45 C 81,49 94,47 98,38 Z" />
              <path d="M 112,24 C 122,16 135,18 139,31 C 129,35 116,33 112,24 Z" />
            </svg>

            <!-- Primary Photo Card: Draggable Before/After Split Slider Box -->
            <div class="wellora-photo-primary results-slider-primary">
              <div class="result-slider-box">
                <!-- Before Image (Base) -->
                <img
                  :src="resultCases[activeResultTab].beforeImage"
                  :alt="resultCases[activeResultTab].beforeLabel"
                  class="slider-img slider-img-before"
                  loading="lazy"
                  decoding="async"
                />

                <!-- After Image (Revealed dynamically by clip-path) -->
                <div
                  class="slider-after-overlay"
                  :style="{ clipPath: `inset(0 0 0 ${sliderPos}%)` }"
                >
                  <img
                    :src="resultCases[activeResultTab].afterImage"
                    :alt="resultCases[activeResultTab].afterLabel"
                    class="slider-img slider-img-after"
                    loading="lazy"
                    decoding="async"
                  />
                </div>

                <!-- Golden Divider Line -->
                <div class="slider-divider-line" :style="{ left: `${sliderPos}%` }">
                  <div class="slider-handle-pill">
                    <span class="handle-arrow">‹</span>
                    <span class="handle-sparkle">✦</span>
                    <span class="handle-arrow">›</span>
                  </div>
                </div>

                <!-- Interactive HTML5 Range Input overlay for natural mouse & touch dragging -->
                <input
                  type="range"
                  min="0"
                  max="100"
                  v-model.number="sliderPos"
                  class="slider-range-controller"
                  aria-label="Deslizar para comparar antes y después"
                />

                <!-- Floating Corner Badges -->
                <div class="slider-corner-badge badge-left" :class="{ 'is-dim': sliderPos < 15 }">
                  <span>Antes</span>
                </div>
                <div class="slider-corner-badge badge-right" :class="{ 'is-dim': sliderPos > 85 }">
                  <span>✦ Armonizado</span>
                </div>

                <div class="slider-hint-pill">
                  <span>↔ Desliza la barra dorada para comparar</span>
                </div>
              </div>

              <!-- Preset quick click buttons -->
              <div class="slider-presets-row">
                <button
                  class="preset-btn"
                  :class="{ active: sliderPos >= 90 }"
                  type="button"
                  @click="setSliderPreset(100)"
                >
                  Ver Antes
                </button>
                <button
                  class="preset-btn"
                  :class="{ active: sliderPos >= 40 && sliderPos <= 60 }"
                  type="button"
                  @click="setSliderPreset(50)"
                >
                  50 / 50 Split
                </button>
                <button
                  class="preset-btn"
                  :class="{ active: sliderPos <= 10 }"
                  type="button"
                  @click="setSliderPreset(0)"
                >
                  Ver Resultado
                </button>
              </div>
            </div>

            <!-- Secondary Overlapping Photo Card (Warm Cabina Spa Detail, matching Image 1) -->
            <div class="wellora-photo-secondary results-secondary-thumb">
              <img
                src="https://images.unsplash.com/photo-1512290923902-8a9f81dc236c?auto=format&fit=crop&w=800&q=85"
                alt="Detalle de armonización facial DermoSTETICA"
                class="wellora-secondary-img"
                loading="lazy"
                decoding="async"
              />
            </div>

            <!-- Rotating Luxury Circular Stamp Badge (matching Image 1) -->
            <div class="wellora-stamp-badge" aria-hidden="true">
              <svg class="stamp-rotating-svg" viewBox="0 0 140 140">
                <path id="stampResultsPath" d="M 70, 70 m -50, 0 a 50,50 0 1,1 100,0 a 50,50 0 1,1 -100,0" fill="none" />
                <text class="stamp-circular-text">
                  <textPath href="#stampResultsPath" startOffset="0%">
                    ★ CASOS REALES ★ DERMOSTETICA SALINAS
                  </textPath>
                </text>
              </svg>
              <div class="stamp-center-icon">
                <svg viewBox="0 0 24 24" class="stamp-leaf-svg" fill="currentColor">
                  <path d="M12 2C8 6 6 10 6 14a6 6 0 0 0 12 0c0-4-2-8-6-12zm0 15a3 3 0 0 1-3-3c0-2.5 1.5-5 3-7 1.5 2 3 4.5 3 7a3 3 0 0 1-3 3z"/>
                </svg>
              </div>
            </div>
          </div>

          <!-- Right Column: Soft Sage/Linen Card with Numbered Accordion Cases (matching Image 1) -->
          <div class="wellora-feature-accordion-card">
            <div class="wellora-accordion-list">
              <div
                v-for="(caseItem, index) in resultCases"
                :key="caseItem.title"
                class="wellora-accordion-item"
                :class="{ 'is-expanded': activeResultTab === index }"
              >
                <button
                  class="wellora-accordion-trigger"
                  type="button"
                  @click="activeResultTab = index; setSliderPreset(50)"
                >
                  <span class="wellora-item-number-title">
                    <strong class="item-index">0{{ index + 1 }}.</strong>
                    <span class="item-label">{{ caseItem.title }}</span>
                  </span>
                  <span class="wellora-chevron-circle" :class="{ 'is-open': activeResultTab === index }" aria-hidden="true">
                    <svg viewBox="0 0 20 20" class="chevron-svg" fill="none" stroke="currentColor">
                      <path d="M5 8l5 5 5-5" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                    </svg>
                  </span>
                </button>

                <!-- Collapsible Content (matching Image 1: description + floral asterisk bullets + quote + cta) -->
                <div v-show="activeResultTab === index" class="wellora-accordion-body">
                  <p class="accordion-body-desc">{{ caseItem.description }}</p>

                  <ul class="accordion-bullets-list">
                    <li>
                      <span class="bullet-asterisk">✻</span>
                      <div>
                        <strong>Protocolo Clínico:</strong> {{ caseItem.subtitle }}
                      </div>
                    </li>
                    <li>
                      <span class="bullet-asterisk">✻</span>
                      <div>
                        <strong>Duración Estimada:</strong> {{ caseItem.stats[1].value }}
                      </div>
                    </li>
                    <li>
                      <span class="bullet-asterisk">✻</span>
                      <div>
                        <strong>Tiempo en Cabina &amp; Recuperación:</strong> {{ caseItem.stats[0].value }} · {{ caseItem.stats[2].value }}
                      </div>
                    </li>
                  </ul>

                  <blockquote class="wellora-accordion-quote">
                    <p>{{ caseItem.quote }}</p>
                    <small>{{ caseItem.doctor }} · Directora Médica</small>
                  </blockquote>

                  <div class="accordion-action-row">
                    <a
                      class="wellora-accordion-cta"
                      :href="getWhatsAppUrl(caseItem.waText)"
                      target="_blank"
                      rel="noreferrer"
                    >
                      <span>Consultar este procedimiento</span>
                      <span class="cta-circle-arrow" aria-hidden="true">↗</span>
                    </a>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Wellora Specialist Team Showcase & Dra. Evelyn González (matching Image 4) -->
      <section id="nosotros" class="wellora-team-section section-wrap">
        <!-- Background Single Continuous Line-Art Face Illustration (matching Image 4 bottom-left) -->
        <div class="wellora-bg-face" aria-hidden="true">
          <svg viewBox="0 0 200 240" fill="none" class="line-art-face-svg">
            <path
              d="M100 20 C 130 20, 160 45, 160 85 C 160 120, 145 150, 125 180 C 110 205, 95 220, 80 230 C 70 215, 60 190, 60 160 C 60 120, 75 80, 85 50 C 90 35, 95 20, 100 20 Z"
              stroke="#C5A059"
              stroke-width="1.2"
              stroke-linecap="round"
              stroke-linejoin="round"
              opacity="0.22"
            />
            <path
              d="M115 70 C 125 68, 135 72, 140 80"
              stroke="#C5A059"
              stroke-width="1"
              stroke-linecap="round"
              opacity="0.25"
            />
            <path
              d="M125 90 C 128 105, 120 120, 115 125 L 125 128"
              stroke="#C5A059"
              stroke-width="1"
              stroke-linecap="round"
              opacity="0.25"
            />
            <path
              d="M110 145 C 120 142, 130 145, 135 152 C 125 156, 115 154, 110 145 Z"
              stroke="#C5A059"
              stroke-width="1"
              opacity="0.22"
            />
            <circle cx="55" cy="190" r="18" stroke="#C5A059" stroke-width="0.8" opacity="0.18" />
            <circle cx="55" cy="210" r="14" stroke="#C5A059" stroke-width="0.8" opacity="0.18" />
          </svg>
        </div>

        <!-- Background Abstract Swirl Flourish (matching Image 4 top-right) -->
        <div class="wellora-bg-swirl" aria-hidden="true">
          <svg viewBox="0 0 180 120" fill="none" class="line-art-swirl-svg">
            <path
              d="M10 80 C 40 10, 90 110, 130 30 C 150 -10, 175 40, 160 80 C 150 110, 120 90, 130 60"
              stroke="#C5A059"
              stroke-width="1.2"
              stroke-linecap="round"
              opacity="0.25"
            />
          </svg>
        </div>

        <div class="section-heading wellora-team-header reveal-on-scroll">
          <div>
            <div class="eyebrow">
              <span class="gold-line"></span>
              <span>EQUIPO MÉDICO &amp; ESPECIALISTAS · SALINAS</span>
            </div>
            <h2>Las Manos Expertas Detrás de<br />tu <em>Armonía &amp; Serenidad.</em></h2>
          </div>
          <div class="heading-aside">
            <a
              class="wellora-discover-cta"
              :href="doctorInstagramUrl"
              target="_blank"
              rel="noreferrer"
            >
              <span>Conoce a la Dra. en Instagram</span>
              <span class="cta-arrow" aria-hidden="true">↗</span>
            </a>
          </div>
        </div>

        <!-- 4 Arched Specialist Cards Grid (matching Image 4) -->
        <div class="wellora-arches-grid reveal-stagger">
          <article
            v-for="member in medicalTeam"
            :key="member.name"
            class="wellora-arch-card"
          >
            <!-- Arched Silhouette Frame -->
            <div class="wellora-arch-media">
              <img
                :src="member.image"
                :alt="member.alt"
                loading="lazy"
                decoding="async"
                class="wellora-arch-img"
              />
              <div class="wellora-arch-overlay"></div>
              <span class="wellora-arch-badge">{{ member.badge }}</span>
            </div>

            <!-- Specialist Typography & Specialty Caption -->
            <div class="wellora-arch-caption">
              <h3 class="wellora-arch-name">{{ member.name }}</h3>
              <span class="wellora-arch-role">{{ member.role }}</span>
              <p class="wellora-arch-spec">{{ member.specialty }}</p>
              <a
                class="wellora-arch-link"
                :href="member.link"
                target="_blank"
                rel="noreferrer"
                :aria-label="`Consultar con ${member.name} en WhatsApp`"
              >
                <span>Consultar</span>
                <span aria-hidden="true">↗</span>
              </a>
            </div>
          </article>
        </div>

        <!-- Dra. Evelyn González Philosophy & Clinical Authority Spotlight -->
        <div class="doctor-spotlight-card reveal-card">
          <div class="doctor-spotlight-inner">
            <div class="spotlight-left">
              <div class="eyebrow">
                <span class="gold-line"></span>
                <span>FILOSOFÍA DE ATENCIÓN</span>
              </div>
              <h3 class="spotlight-title">«La confianza también es parte fundamental del <em>tratamiento.»</em></h3>
              <p class="spotlight-quote">
                Soy la <strong>Dra. Evelyn González</strong>, médica especialista en medicina estética y láser con más de diez años de trayectoria en Ecuador. Mi filosofía se basa en potenciar lo mejor de cada paciente con criterio anatómico estricto, productos certificados internacionalmente y una profunda empatía humana.
              </p>

              <div class="doctor-pillars">
                <div class="pillar">
                  <span class="pillar-bullet">✓</span>
                  <div>
                    <strong>Inyector Certificado BOTOX &amp; Fillers</strong>
                    <p>Armonizaciones faciales precisas, simétricas y sutiles.</p>
                  </div>
                </div>
                <div class="pillar">
                  <span class="pillar-bullet">✓</span>
                  <div>
                    <strong>Tecnología Láser Alma Harmony de Punta</strong>
                    <p>Tratamientos no ablativos con respaldo clínico internacional.</p>
                  </div>
                </div>
                <div class="pillar">
                  <span class="pillar-bullet">✓</span>
                  <div>
                    <strong>Atención Exclusiva en Salinas</strong>
                    <p>Consultas privadas, personalizadas y sin tiempos de espera.</p>
                  </div>
                </div>
              </div>

              <div class="about-signature-box">
                <div class="signature-emblem">D.</div>
                <div>
                  <strong>Dra. Evelyn González</strong>
                  <small>Medicina Estética &amp; Láser · @dra_evelyngonzalez</small>
                </div>
              </div>

              <div class="about-actions">
                <a class="button button-gold" :href="defaultWhatsAppUrl" target="_blank" rel="noreferrer">
                  Agenda tu consulta personalizada ↗
                </a>
                <a class="button button-outline" :href="doctorInstagramUrl" target="_blank" rel="noreferrer">
                  Conoce a la Dra. en Instagram ↗
                </a>
              </div>
            </div>

            <div class="spotlight-right">
              <div class="spotlight-portrait-arch">
                <img
                  src="https://images.unsplash.com/photo-1559839734-2b71ea197ec2?auto=format&fit=crop&w=1000&q=85"
                  alt="Dra. Evelyn González en DermoSTETICA Salinas"
                  loading="lazy"
                  decoding="async"
                />
                <div class="spotlight-portrait-badge">
                  <span class="gold-sparkle">✦</span>
                  <span>+10 AÑOS FORMANDO SONRISAS Y CONFIANZA</span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Wellora-Inspired Clinic Sede Salinas & Benefits with Frosted Hours Photo (matching Image 2) -->
      <section id="clinica" class="wellora-benefits-section section-wrap">
        <!-- Background delicate line-art woman in towel / spa sketch (matching Image 2 bottom-left) -->
        <div class="benefits-bg-sketch" aria-hidden="true">
          <svg viewBox="0 0 220 260" fill="none" class="sketch-woman-svg">
            <path d="M110 30 C 80 30, 60 55, 60 90 C 60 115, 75 140, 85 170 C 95 200, 75 220, 50 240" stroke="#C5A059" stroke-width="1" stroke-linecap="round" opacity="0.18" />
            <path d="M110 30 C 140 30, 160 55, 160 90 C 160 115, 145 140, 135 170 C 125 200, 145 220, 170 240" stroke="#C5A059" stroke-width="1" stroke-linecap="round" opacity="0.18" />
            <path d="M75 80 C 90 70, 130 70, 145 80" stroke="#C5A059" stroke-width="1" stroke-linecap="round" opacity="0.2" />
            <path d="M90 120 C 100 115, 120 115, 130 120" stroke="#C5A059" stroke-width="1" stroke-linecap="round" opacity="0.2" />
            <path d="M100 145 C 106 142, 114 142, 120 145" stroke="#C5A059" stroke-width="1" stroke-linecap="round" opacity="0.2" />
            <path d="M65 60 C 90 40, 130 40, 155 60 C 170 75, 165 95, 155 105" stroke="#C5A059" stroke-width="1.2" stroke-linecap="round" opacity="0.22" />
          </svg>
        </div>

        <div class="wellora-benefits-header reveal-on-scroll">
          <div class="benefits-header-main">
            <div class="eyebrow">
              <span class="gold-line"></span>
              <span>SEDE SALINAS · BENEFICIOS CLÍNICOS</span>
            </div>
            <h2>Experimenta los verdaderos beneficios<br />de una <em>atención médica serena.</em></h2>
          </div>
          <div class="benefits-header-aside">
            <p>
              Desde revitalización dérmica hasta armonizaciones completas sin cirugía, diseñamos un espacio de confort exclusivo en Salinas donde tu piel recibe la más alta seguridad médica.
            </p>
            <a class="wellora-capsule-cta" :href="secondaryWhatsAppUrl" target="_blank" rel="noreferrer">
              <span>Agendar en Salinas</span>
              <span class="capsule-cta-circle" aria-hidden="true">↗</span>
            </a>
          </div>
        </div>

        <div class="wellora-benefits-grid reveal-stagger">
          <!-- Benefit Card 1 -->
          <article class="benefit-card">
            <div class="benefit-icon-box" aria-hidden="true">
              <!-- Spa Bed / Clinical Cabina Icon -->
              <svg viewBox="0 0 48 48" class="benefit-svg" fill="none" stroke="currentColor">
                <path d="M8 26h32M8 26c0-4.4 3.6-8 8-8h16c4.4 0 8 3.6 8 8M10 26v12M38 26v12M14 18V12h6v6" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/>
                <path d="M8 32h32" stroke-width="1.4" stroke-dasharray="2 3"/>
              </svg>
            </div>
            <h3 class="benefit-title">Salud &amp; Firmeza Dérmica</h3>
            <p class="benefit-desc">
              Tratamientos médicos que mejoran la microcirculación, oxigenan los tejidos y estimulan colágeno nuevo para devolver la luminosidad y elasticidad natural a tu piel.
            </p>
            <div class="benefit-divider"></div>
            <ul class="benefit-bullets">
              <li><span class="bullet-asterisk">✳</span> Protocolos que activan colágeno y elastina dérmica</li>
              <li><span class="bullet-asterisk">✳</span> Limpiezas médicas profundas e hidratación intensiva</li>
            </ul>
          </article>

          <!-- Benefit Card 2 -->
          <article class="benefit-card">
            <div class="benefit-icon-box" aria-hidden="true">
              <!-- Deep Serenity / Gentle Hands Icon -->
              <svg viewBox="0 0 48 48" class="benefit-svg" fill="none" stroke="currentColor">
                <path d="M24 14c-4.4 0-8 3.6-8 8v4c0 4.4 3.6 8 8 8s8-3.6 8-8v-4c0-4.4-3.6-8-8-8z" stroke-width="1.8" stroke-linecap="round"/>
                <path d="M14 22c-2 0-4 1.5-4 4l-1 6c-.5 2 1 3.5 3 3.5h2M34 22c2 0 4 1.5 4 4l1 6c.5 2-1 3.5-3 3.5h-2" stroke-width="1.6" stroke-linecap="round"/>
                <path d="M20 38c1.5 1.5 3 2 4 2s2.5-.5 4-2" stroke-width="1.6" stroke-linecap="round"/>
              </svg>
            </div>
            <h3 class="benefit-title">Privacidad &amp; Calma en Cabina</h3>
            <p class="benefit-desc">
              Consultas exclusivas programadas sin salas de espera concurridas ni prisas. Un entorno de aromaterapia y descanso pensado para que desconectes del estrés diario.
            </p>
            <div class="benefit-divider"></div>
            <ul class="benefit-bullets">
              <li><span class="bullet-asterisk">✳</span> Citas reservadas exclusivamente para ti sin esperas</li>
              <li><span class="bullet-asterisk">✳</span> Aromas envolventes y atmósfera de máxima serenidad</li>
            </ul>
          </article>

          <!-- Benefit Card 3: Photo with Floating Frosted Glass Working Hours Card (matching Image 2) -->
          <div class="benefit-photo-frame">
            <img
              src="https://images.unsplash.com/photo-1522337360788-8b13dee7a37e?auto=format&fit=crop&w=900&q=85"
              alt="Atención médica y relajación en DermoSTETICA Salinas"
              class="benefit-photo-img"
              loading="lazy"
              decoding="async"
            />
            <div class="benefit-photo-gradient" aria-hidden="true"></div>

            <!-- Floating Frosted Glass Working Hours Card (Glassmorphic) -->
            <div class="floating-hours-glass">
              <div class="hours-header">
                <svg viewBox="0 0 24 24" class="hours-clock-icon" fill="none" stroke="currentColor">
                  <circle cx="12" cy="12" r="9" stroke-width="1.8"/>
                  <path d="M12 7v5l3 3" stroke-width="1.8" stroke-linecap="round"/>
                </svg>
                <strong class="hours-title">Horarios de Atención</strong>
              </div>
              <div class="hours-schedule">
                <div class="schedule-row">
                  <span class="schedule-day">Lunes a Viernes:</span>
                  <span class="schedule-time">09H00 a 19H00</span>
                </div>
                <div class="schedule-row">
                  <span class="schedule-day">Sábados:</span>
                  <span class="schedule-time">09H00 a 16H00</span>
                </div>
                <div class="schedule-row schedule-row-booking">
                  <span class="schedule-day">Reserva directa:</span>
                  <a :href="getWhatsAppUrl('Hola DermoSTETICA, deseo consultar disponibilidad de cita en Salinas.', whatsappNumber2)" target="_blank" class="schedule-phone-highlight">095 983 2254</a>
                </div>
              </div>
              <div class="hours-location-tag">
                <span>📍 Av. Carlos Espinoza Larrea (frente a Supermaxi, Salinas)</span>
              </div>
            </div>
          </div>
        </div>

        <!-- Row of Treatment Pill Badges (matching Image 2 pills row) -->
        <div class="benefits-tags-strip reveal-on-scroll">
          <a href="#tratamientos" class="tag-pill-item">• Toxina Botulínica (Botox)</a>
          <a href="#tratamientos" class="tag-pill-item">• Perfilado &amp; Volumen Labial</a>
          <a href="#tratamientos" class="tag-pill-item">• Rinomodelación sin Cirugía</a>
          <a href="#protocolos" class="tag-pill-item">• Láser ClearLift™ Harmony XL</a>
          <a href="#protocolos" class="tag-pill-item">• Enzimas PB Serum Faciales</a>
          <a href="#tratamientos" class="tag-pill-item">• Bioestimuladores de Colágeno</a>
        </div>

        <!-- Bottom Contact & Quotation Bar -->
        <div class="benefits-trust-footer reveal-on-scroll">
          <div class="trust-footer-duo">
            <img src="https://images.unsplash.com/photo-1559839734-2b71ea197ec2?auto=format&fit=crop&w=120&q=80" alt="Dra. Evelyn González" class="trust-avatar-small" loading="lazy" decoding="async"/>
            <div class="trust-phone-small" aria-hidden="true">📞</div>
          </div>
          <p class="trust-footer-text">
            Construyamos juntos tu mejor plan de armonización facial.
            <a :href="secondaryWhatsAppUrl" target="_blank" rel="noreferrer" class="trust-footer-link">Solicitar Información o Cita ↗</a>
          </p>
        </div>
      </section>

      <!-- Wellora-Inspired "Why Choose Us" / Trayectoria Desde 2015 (matching Image 4) -->
      <section id="proceso" class="wellora-why-section section-wrap">
        <div class="why-layout reveal-card">
          <!-- Left Visual Column with Stacked Photos & Dark "Since 2015" Badge (matching Image 2) -->
          <div class="why-visual-col">
            <!-- Decorative Dot Matrix Pattern (matching Image 2) -->
            <div class="why-dot-matrix" aria-hidden="true"></div>

            <!-- Golden 4-point Sparkle Accent (matching Image 2) -->
            <span class="why-sparkle" aria-hidden="true">✦</span>

            <!-- Top Photo (Rounded frame) -->
            <div class="why-photo-top">
              <img
                src="https://images.unsplash.com/photo-1515377905703-c4788e51af15?auto=format&fit=crop&w=800&q=85"
                alt="Piel cuidada y relajada en DermoSTETICA"
                loading="lazy"
                decoding="async"
                class="why-img"
              />
            </div>

            <!-- Bottom Photo (White framed rounded photo matching Image 2) -->
            <div class="why-photo-bottom">
              <img
                src="https://images.unsplash.com/photo-1570172619644-dfd03ed5d881?auto=format&fit=crop&w=800&q=85"
                alt="Tratamiento facial en cabina DermoSTETICA Salinas"
                loading="lazy"
                decoding="async"
                class="why-img"
              />
            </div>

            <!-- Floating Noir Badge: "Clínica Médica / Desde / 2015" (matching Image 2) -->
            <div class="why-floating-badge">
              <div class="badge-emblem-icon" aria-hidden="true">
                <!-- Folded towel / layer ribbon emblem -->
                <svg viewBox="0 0 32 32" class="badge-svg" fill="none" stroke="currentColor">
                  <path d="M8 8h16c2.2 0 4 1.8 4 4s-1.8 4-4 4H8c-2.2 0-4-1.8-4-4s1.8-4 4-4z" stroke-width="1.8"/>
                  <path d="M8 16h16c2.2 0 4 1.8 4 4s-1.8 4-4 4H8c-2.2 0-4-1.8-4-4s1.8-4 4-4z" stroke-width="1.8"/>
                </svg>
              </div>
              <span class="badge-since-label">Clínica Médica<br />Desde</span>
              <strong class="badge-year">2015</strong>
            </div>
          </div>

          <!-- Right Content Column with Dual Highlight Cards (Cream + Noir) -->
          <div class="why-content-col">
            <div class="eyebrow">
              <span class="gold-line"></span>
              <span>POR QUÉ ELEGIR DERMOSTETICA</span>
            </div>
            <h2 class="why-main-title">El espacio ideal para tu<br /><em>bienestar y rejuvenecimiento.</em></h2>
            <p class="why-lead-desc">
              Desde equipamiento láser de estándar internacional hasta atención médica personalizada y cercana, nos enfocamos en brindarte tratamientos que promueven la relajación y armonizan tus rasgos con total seguridad médica.
            </p>

            <!-- Dual Highlight Cards Row (matching Image 4) -->
            <div class="why-dual-cards">
              <!-- Left: Cream Highlight Card -->
              <div class="why-card-cream">
                <h4 class="card-cream-title">Inyectores Médicos Certificados</h4>
                <p class="card-cream-text">
                  La Dra. Evelyn González y su equipo cuentan con acreditación médica continua y dominio de técnicas de inyección seguras.
                </p>
                <div class="card-cream-divider"></div>
                <div class="card-cream-bullet">
                  <span class="card-asterisk">✳</span>
                  <span>Cada sesión prioriza tu confort y naturalidad</span>
                </div>
              </div>

              <!-- Right: Noir Dark Highlight Card (matching Image 4) -->
              <div class="why-card-noir">
                <div class="card-noir-header">
                  <strong class="noir-stat">+10</strong>
                  <span class="noir-stat-label">Años de<br />Experiencia</span>
                </div>
                <div class="card-noir-icon" aria-hidden="true">
                  <!-- Spine / Body Harmony Line Art Icon -->
                  <svg viewBox="0 0 48 48" class="noir-icon-svg" fill="none" stroke="currentColor">
                    <circle cx="24" cy="8" r="4" stroke-width="1.6"/>
                    <path d="M24 16v24M16 22c4-2 12-2 16 0M14 30c5-2 15-2 20 0M18 38c3-1 9-1 12 0" stroke-width="1.6" stroke-linecap="round"/>
                  </svg>
                </div>
                <p class="card-noir-desc">
                  Más de una década de experiencia cuidando la salud dérmica y estética en Salinas y la provincia de Santa Elena.
                </p>
              </div>
            </div>

            <!-- Action Button -->
            <div class="why-actions">
              <a class="wellora-capsule-cta" :href="defaultWhatsAppUrl" target="_blank" rel="noreferrer">
                <span>Conoce Nuestros Protocolos</span>
                <span class="capsule-cta-circle" aria-hidden="true">↗</span>
              </a>
            </div>
          </div>
        </div>
      </section>

      <!-- Infinite Slow-Moving Panoramic Ribbon Gallery (matching Image 1) -->
      <section id="galeria" class="infinite-gallery-section" aria-label="Galería de experiencia en cabina">
        <div class="section-wrap">
          <div class="gallery-header reveal-on-scroll">
            <div>
              <div class="eyebrow">
                <span class="gold-line"></span>
                <span>COMUNIDAD &amp; MOMENTOS EN CABINA</span>
              </div>
              <h2>Un oasis de serenidad<br />para tu <em>bienestar.</em></h2>
            </div>
            <div class="gallery-header-aside">
              <p>Cada rincón de nuestra clínica en Salinas está concebido para desconectar del estrés y realzar tu armonía con seguridad médica.</p>
              <a :href="instagramUrl" target="_blank" rel="noreferrer" class="gallery-insta-pill">
                <svg viewBox="0 0 24 24" class="gallery-insta-svg" fill="currentColor">
                  <path d="M12 2.163c3.204 0 3.584.012 4.85.07 3.252.148 4.771 1.691 4.919 4.919.058 1.265.069 1.645.069 4.849 0 3.205-.012 3.584-.069 4.849-.149 3.225-1.664 4.771-4.919 4.919-1.266.058-1.644.07-4.85.07-3.204 0-3.584-.012-4.849-.07-3.26-.149-4.771-1.699-4.919-4.92-.058-1.265-.07-1.644-.07-4.849 0-3.204.013-3.583.07-4.849.149-3.227 1.664-4.771 4.919-4.919 1.266-.057 1.645-.069 4.849-.069zm0-2.163c-3.259 0-3.667.014-4.947.072-4.358.2-6.78 2.618-6.98 6.98-.059 1.281-.073 1.689-.073 4.948 0 3.259.014 3.668.072 4.948.2 4.358 2.618 6.78 6.98 6.98 1.281.058 1.689.072 4.948.072 3.259 0 3.668-.014 4.948-.072 4.354-.2 6.782-2.618 6.979-6.98.059-1.28.073-1.689.073-4.948 0-3.259-.014-3.667-.072-4.947-.196-4.354-2.617-6.78-6.979-6.98-1.281-.059-1.69-.073-4.949-.073zm0 5.838c-3.403 0-6.162 2.759-6.162 6.162s2.759 6.163 6.162 6.163 6.162-2.759 6.162-6.163c0-3.403-2.759-6.162-6.162-6.162zm0 10.162c-2.209 0-4-1.79-4-4 0-2.209 1.791-4 4-4s4 1.791 4 4c0 2.21-1.791 4-4 4zm6.406-11.845c-.796 0-1.441.645-1.441 1.44s.645 1.44 1.441 1.44c.795 0 1.439-.645 1.439-1.44s-.644-1.44-1.439-1.44z"/>
                </svg>
                <span>Seguir @dermostetica_ec</span>
                <span aria-hidden="true">↗</span>
              </a>
            </div>
          </div>
        </div>

        <!-- Full-width infinite slow-scrolling marquee ribbon -->
        <div class="infinite-gallery-wrapper">
          <div class="infinite-gallery-track">
            <!-- Set 1 -->
            <div
              v-for="(photo, index) in slowGallery"
              :key="'g1-' + index"
              class="gallery-photo-card"
            >
              <img :src="photo.image" :alt="photo.alt" loading="lazy" decoding="async" class="gallery-photo-img" />
              <div class="gallery-photo-overlay">
                <span class="gallery-photo-tag">{{ photo.tag }}</span>
              </div>
            </div>
            <!-- Set 2 (seamless duplication for infinite loop) -->
            <div
              v-for="(photo, index) in slowGallery"
              :key="'g2-' + index"
              class="gallery-photo-card"
              aria-hidden="true"
            >
              <img :src="photo.image" :alt="photo.alt" loading="lazy" decoding="async" class="gallery-photo-img" />
              <div class="gallery-photo-overlay">
                <span class="gallery-photo-tag">{{ photo.tag }}</span>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Luxury Noir & Gold Booking Banner -->
      <section class="booking-banner reveal-card">
        <div class="booking-decoration" aria-hidden="true">✦</div>
        <div class="booking-badge">
          <span>ATENCIÓN EXCLUSIVA EN SALINAS</span>
        </div>
        <h2>Empieza por una<br /><em>conversación.</em></h2>
        <p>Estamos listas para escucharte y ayudarte a resaltar tu armonía natural con la máxima seguridad médica.</p>

        <div class="booking-cta-group">
          <a class="button button-gold-bright" :href="defaultWhatsAppUrl" target="_blank" rel="noreferrer">
            <span>Contactar por WhatsApp</span>
            <span aria-hidden="true">↗</span>
          </a>
          <a class="button button-white-outline" :href="bookingUrl" target="_blank" rel="noreferrer">
            <span>Ver enlaces en Taplink</span>
            <span aria-hidden="true">↗</span>
          </a>
        </div>
        <span class="booking-footnote">DermoSTETICA · Dra. Evelyn González · +10 años de experiencia</span>
      </section>

      <!-- Wellora-Inspired Numbered Luxury Accordion & Overlapping Photos with Rotating Stamp (matching Image 3) -->
      <section id="preguntas" class="wellora-faq-section section-wrap">
        <div class="wellora-faq-header reveal-on-scroll">
          <div class="eyebrow">
            <span class="gold-line"></span>
            <span>PREGUNTAS FRECUENTES &amp; PROTOCOLOS</span>
          </div>
          <h2>Criterio médico que resuelve<br />tus dudas <em>antes de tu visita.</em></h2>
          <p class="faq-lead">
            Desde la experiencia de nuestro equipo hasta el entorno exclusivo en Salinas, nuestras características principales están diseñadas para brindarte total tranquilidad y resultados armónicos.
          </p>
        </div>

        <div class="wellora-faq-workbench reveal-card">
          <!-- Left Overlapping Photos with Rotating Gold Stamp Badge (matching Image 3) -->
          <div class="faq-visual-side">
            <!-- Botanical Leaf Branch Line Art in Background (matching Image 3) -->
            <div class="faq-botanical-bg" aria-hidden="true">
              <svg viewBox="0 0 160 220" fill="none" class="botanical-branch-svg">
                <path d="M80 210 C 80 150, 75 90, 95 30" stroke="#C5A059" stroke-width="1.2" stroke-linecap="round" opacity="0.3"/>
                <path d="M82 170 C 60 160, 50 145, 55 135 C 65 130, 80 145, 82 165" stroke="#C5A059" stroke-width="1" stroke-linecap="round" opacity="0.25"/>
                <path d="M85 130 C 110 120, 120 105, 115 95 C 105 90, 90 105, 85 125" stroke="#C5A059" stroke-width="1" stroke-linecap="round" opacity="0.25"/>
                <path d="M88 90 C 70 80, 65 65, 70 55 C 80 50, 90 65, 90 85" stroke="#C5A059" stroke-width="1" stroke-linecap="round" opacity="0.25"/>
                <path d="M92 50 C 110 40, 115 25, 110 15 C 100 15, 92 30, 92 45" stroke="#C5A059" stroke-width="1" stroke-linecap="round" opacity="0.25"/>
              </svg>
            </div>

            <!-- Top Photo (Corner rounded) -->
            <div class="faq-photo-top">
              <img
                src="https://images.unsplash.com/photo-1540555700478-4be289fbecef?auto=format&fit=crop&w=800&q=85"
                alt="Paciente recibiendo armonización y masaje facial en cabina"
                loading="lazy"
                decoding="async"
                class="faq-img"
              />
            </div>

            <!-- Bottom Photo (Corner rounded) -->
            <div class="faq-photo-bottom">
              <img
                src="https://images.unsplash.com/photo-1512290923902-8a9f81dc236c?auto=format&fit=crop&w=800&q=85"
                alt="Relajación y serenidad estética en Salinas"
                loading="lazy"
                decoding="async"
                class="faq-img"
              />
            </div>

            <!-- Circular Rotating Gold Stamp Badge at Intersection (matching Image 3) -->
            <a
              class="faq-rotating-stamp"
              :href="defaultWhatsAppUrl"
              target="_blank"
              rel="noreferrer"
              aria-label="Contactar a DermoSTETICA en WhatsApp"
            >
              <div class="stamp-svg-wrap">
                <svg viewBox="0 0 140 140" class="rotating-text-svg">
                  <path
                    id="stampCirclePath"
                    d="M 70, 70 m -50, 0 a 50,50 0 1,1 100,0 a 50,50 0 1,1 -100,0"
                    fill="none"
                  />
                  <text class="stamp-text">
                    <textPath href="#stampCirclePath" startOffset="0%">
                      ✦ DERMOSTETICA ✦ SALINAS ECUADOR ✦ DRA. EVELYN ✦
                    </textPath>
                  </text>
                </svg>
                <div class="stamp-center-emblem" aria-hidden="true">
                  <svg viewBox="0 0 24 24" class="stamp-leaf-svg" fill="none" stroke="currentColor">
                    <path d="M12 2C8 7 8 13 12 18C16 13 16 7 12 2Z" stroke-width="1.8" stroke-linejoin="round"/>
                    <path d="M12 22V18" stroke-width="1.8" stroke-linecap="round"/>
                  </svg>
                </div>
              </div>
            </a>
          </div>

          <!-- Right Side: Numbered Luxury Accordion (01, 02, 03, 04, 05) -->
          <div class="faq-accordion-side">
            <div class="accordion-list">
              <article
                v-for="(faq, index) in faqs"
                :key="faq.num"
                class="accordion-item"
                :class="{ 'is-open': activeFaq === index }"
              >
                <button
                  class="accordion-header-btn"
                  type="button"
                  :aria-expanded="activeFaq === index"
                  @click="activeFaq = activeFaq === index ? null : index"
                >
                  <div class="accordion-num-title">
                    <span class="item-num">{{ faq.num }}.</span>
                    <span class="item-question">{{ faq.question }}</span>
                  </div>
                  <div class="accordion-chevron-circle" aria-hidden="true">
                    <svg viewBox="0 0 24 24" class="chevron-svg" fill="none" stroke="currentColor">
                      <path d="M6 9l6 6 6-6" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                    </svg>
                  </div>
                </button>

                <div v-show="activeFaq === index" class="accordion-body-collapse">
                  <p class="accordion-lead-text">{{ faq.answer }}</p>
                  <ul v-if="faq.bullets && faq.bullets.length" class="accordion-bullet-list">
                    <li v-for="bullet in faq.bullets" :key="bullet">
                      <span class="accordion-asterisk">✳</span>
                      <span>{{ bullet }}</span>
                    </li>
                  </ul>
                </div>
              </article>
            </div>
          </div>
        </div>

        <!-- Bottom Trust Badge Strip (matching Image 3 bottom) -->
        <div class="wellora-trust-bar reveal-on-scroll">
          <div class="trust-rating-box">
            <span class="trust-rating-label">Avalado por <strong>+2,500 Pacientes Satisfechos</strong></span>
            <div class="trust-stars" aria-label="Calificación 4.9 de 5 estrellas">
              <span>★</span><span>★</span><span>★</span><span>★</span><span>★</span>
              <strong class="trust-score">4.9 / 5</strong>
            </div>
          </div>
          <div class="trust-contact-pill">
            <div class="trust-avatar-duo">
              <img src="https://images.unsplash.com/photo-1559839734-2b71ea197ec2?auto=format&fit=crop&w=120&q=80" alt="Dra. Evelyn González" class="trust-avatar-img" loading="lazy" decoding="async"/>
              <div class="trust-phone-badge" aria-hidden="true">
                <svg viewBox="0 0 24 24" class="trust-phone-svg" fill="none" stroke="currentColor">
                  <path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z" stroke-width="1.8"/>
                </svg>
              </div>
            </div>
            <p class="trust-invite">
              ¿Tienes una duda sobre tu piel?
              <a :href="defaultWhatsAppUrl" target="_blank" rel="noreferrer" class="trust-invite-link">Consulta directamente con la Dra. Evelyn ↗</a>
            </p>
          </div>
        </div>
      </section>
    </main>

    <!-- Official Brand Footer -->
    <footer class="site-footer reveal-on-scroll">
      <div class="footer-main">
        <div class="footer-brand">
          <a class="brand-link footer-brand-link" href="#inicio">
            <div class="brand-emblem-official" aria-hidden="true">
              <svg viewBox="0 0 54 54" class="brand-emblem-svg" fill="none">
                <defs>
                  <linearGradient id="footerBrandGoldStem" x1="0%" y1="0%" x2="100%" y2="100%">
                    <stop offset="0%" stop-color="#FDD87F" />
                    <stop offset="38%" stop-color="#FA9E00" />
                    <stop offset="72%" stop-color="#E59000" />
                    <stop offset="100%" stop-color="#BD7400" />
                  </linearGradient>
                </defs>
                <circle cx="27" cy="27" r="25.5" fill="#1C1A18" stroke="url(#footerBrandGoldStem)" stroke-width="1.3" />
                <circle cx="27" cy="27" r="23.5" fill="none" stroke="url(#footerBrandGoldStem)" stroke-width="0.5" stroke-opacity="0.35" />
                <circle cx="22.5" cy="11" r="1.1" fill="url(#footerBrandGoldStem)" />
                <circle cx="27" cy="9.8" r="1.4" fill="url(#footerBrandGoldStem)" />
                <circle cx="31.5" cy="11" r="1.1" fill="url(#footerBrandGoldStem)" />
                <path
                  d="M 16 14.5 h 8.5 c 7.2 0 12.2 4 12.2 12.2 c 0 8.2 -5 12.2 -12.2 12.2 h -8.5 z m 4.8 20.2 h 3.5 c 4.6 0 7.5 -2.4 7.5 -7.8 c 0 -5.4 -2.9 -7.8 -7.5 -7.8 h -3.5 z"
                  fill="url(#footerBrandGoldStem)"
                />
                <path
                  d="M 24.5 25.5 C 26.5 22, 31.5 22, 32 26 C 32.5 30.5, 26.5 31.5, 23 34 C 27 35.8, 31 38.5, 27.5 41.5 C 24 43.5, 21 39.5, 24 35.5"
                  fill="none"
                  stroke="url(#footerBrandGoldStem)"
                  stroke-width="1.6"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                />
              </svg>
            </div>
            <div class="brand-text-official">
              <div class="brand-title-official">
                <span class="brand-dermo">Dermo</span><span class="brand-estetica">Stetica</span>
              </div>
              <span class="brand-tagline-official">MEDICINA ESTÉTICA Y LÁSER</span>
            </div>
          </a>
          <p class="footer-tagline">Tu belleza, en armonía contigo.</p>
          <div class="footer-social-links">
            <a :href="instagramUrl" target="_blank" rel="noreferrer" class="social-badge">
              Instagram @dermostetica_ec
            </a>
            <a :href="doctorInstagramUrl" target="_blank" rel="noreferrer" class="social-badge">
              @dra_evelyngonzalez
            </a>
          </div>
        </div>

        <div class="footer-column">
          <span class="footer-label">TRATAMIENTOS</span>
          <a href="#aniversario">Promociones 11 Aniversario</a>
          <a href="#tratamientos">Toxina Botulínica (Botox)</a>
          <a href="#tratamientos">Ácido Hialurónico Labios</a>
          <a href="#tratamientos">Rinomodelación sin Cirugía</a>
          <a href="#tratamientos">Láser Harmony XL</a>
          <a href="#tratamientos">EMFUSION Revitalizante</a>
        </div>

        <div class="footer-column">
          <span class="footer-label">CLÍNICA SALINAS</span>
          <p class="footer-info-text">Av. principal Carlos Espinoza Larrea (Frente a Supermaxi)</p>
          <p class="footer-info-text"><strong>Lun - Vie:</strong> 09H00 a 19H00</p>
          <p class="footer-info-text"><strong>Sábados:</strong> 09H00 a 16H00</p>
          <p class="footer-info-text">WhatsApp: 095 983 2254 · 097 931 0507</p>
          <p class="footer-info-text">Fijo: (04) 277-5722</p>
          <a :href="mapsUrl" target="_blank" rel="noreferrer" class="footer-link-gold">Ver mapa de ubicación ↗</a>
        </div>

        <div class="footer-column">
          <span class="footer-label">AGENDA &amp; CONTACTO</span>
          <a :href="defaultWhatsAppUrl" target="_blank" rel="noreferrer" class="footer-link-gold">WhatsApp Citas ↗</a>
          <a :href="bookingUrl" target="_blank" rel="noreferrer">Taplink Oficial ↗</a>
          <a href="#preguntas">Preguntas Frecuentes</a>
          <a class="footer-top" href="#inicio" aria-label="Volver arriba">↑</a>
        </div>
      </div>

      <div class="footer-bottom">
        <span>© 2026 DermoSTETICA S.A.S. · Dra. Evelyn González · Salinas, Ecuador</span>
        <span>Medicina estética responsable y resultados naturales.</span>
      </div>
    </footer>

    <!-- Floating WhatsApp Luxury Button -->
    <a
      class="floating-whatsapp"
      :href="defaultWhatsAppUrl"
      target="_blank"
      rel="noreferrer"
      aria-label="Abrir WhatsApp para agendar una cita"
    >
      <div class="floating-whatsapp-pulse"></div>
      <div class="floating-whatsapp-inner">
        <svg viewBox="0 0 24 24" class="floating-wa-icon" fill="currentColor">
          <path d="M17.472 14.382c-.301-.15-1.78-.878-2.056-.979-.275-.101-.476-.15-.677.15-.2.302-.777.979-.953 1.18-.175.201-.351.226-.652.075-.301-.15-1.272-.469-2.423-1.496-.895-.798-1.5-1.784-1.675-2.086-.176-.301-.019-.464.132-.614.136-.135.301-.351.452-.527.15-.175.2-.301.301-.502.101-.2.05-.376-.025-.527-.076-.151-.678-1.632-.929-2.235-.245-.588-.494-.508-.678-.518-.175-.008-.376-.01-.577-.01-.201 0-.527.075-.802.376s-1.054 1.029-1.054 2.511c0 1.482 1.079 2.912 1.23 3.113.15.201 2.124 3.243 5.145 4.547.719.31 1.28.496 1.718.635.722.23 1.379.197 1.9.119.58-.088 1.78-.727 2.031-1.43.251-.703.251-1.305.176-1.43-.075-.126-.276-.201-.577-.352zM12 2C6.477 2 2 6.477 2 12c0 1.891.524 3.66 1.434 5.176L2 22l4.965-1.399C8.423 21.503 10.15 22 12 22c5.523 0 10-4.477 10-10S17.523 2 12 2zm0 18.2c-1.65 0-3.18-.518-4.44-1.405l-.318-.224-2.951.831.848-2.871-.246-.339A8.17 8.17 0 0 1 3.8 12c0-4.521 3.679-8.2 8.2-8.2 4.521 0 8.2 3.679 8.2 8.2 0 4.521-3.679 8.2-8.2 8.2z"/>
        </svg>
        <span class="floating-wa-text">Agendar en Salinas</span>
      </div>
    </a>

    <!-- Interactive Instagram Story Modal -->
    <div
      v-if="activeStory"
      class="story-modal-overlay"
      role="dialog"
      aria-modal="true"
      @click.self="closeStory"
    >
      <div class="story-modal-card">
        <button class="story-modal-close" type="button" aria-label="Cerrar historia" @click="closeStory">
          ✕
        </button>
        <!-- Story Navigation Arrows -->
        <button class="story-nav-btn story-nav-prev" type="button" aria-label="Historia anterior" @click.stop="prevStory">
          ‹
        </button>
        <button class="story-nav-btn story-nav-next" type="button" aria-label="Siguiente historia" @click.stop="nextStory">
          ›
        </button>
        <div class="story-modal-progress">
          <div :key="activeStory.id" class="story-modal-bar"></div>
        </div>
        <div class="story-modal-header">
          <div class="story-avatar-official">
            <svg viewBox="0 0 54 54" class="story-avatar-svg" fill="none">
              <circle cx="27" cy="27" r="25.5" fill="#FFFFFF" stroke="#FA9E00" stroke-width="1.2" />
              <circle cx="22.5" cy="11" r="1.1" fill="#FA9E00" />
              <circle cx="27" cy="9.8" r="1.4" fill="#FA9E00" />
              <circle cx="31.5" cy="11" r="1.1" fill="#FA9E00" />
              <path
                d="M 16 14.5 h 8.5 c 7.2 0 12.2 4 12.2 12.2 c 0 8.2 -5 12.2 -12.2 12.2 h -8.5 z m 4.8 20.2 h 3.5 c 4.6 0 7.5 -2.4 7.5 -7.8 c 0 -5.4 -2.9 -7.8 -7.5 -7.8 h -3.5 z"
                fill="#FA9E00"
              />
              <path
                d="M 24.5 25.5 C 26.5 22, 31.5 22, 32 26 C 32.5 30.5, 26.5 31.5, 23 34 C 27 35.8, 31 38.5, 27.5 41.5 C 24 43.5, 21 39.5, 24 35.5"
                fill="none"
                stroke="#FA9E00"
                stroke-width="1.6"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
            </svg>
          </div>
          <div class="story-header-text">
            <div class="story-header-brand">
              <strong>DermoStetica</strong>
              <span class="story-verified-badge" title="Cuenta oficial verificada">✓</span>
            </div>
            <small>{{ activeStory.tag }} · Salinas, Ecuador</small>
          </div>
        </div>

        <!-- Layout 1: Official Clinic Schedule Story (matching user image) -->
        <div v-if="activeStory.type === 'schedule'" class="story-schedule-body">
          <div class="story-schedule-card">
            <div class="schedule-card-top">
              <div class="schedule-brand-lockup">
                <div class="schedule-brand-mark">
                  <svg viewBox="0 0 54 54" class="schedule-mark-svg" fill="none">
                    <circle cx="22.5" cy="11" r="1.1" fill="#FFFFFF" />
                    <circle cx="27" cy="9.8" r="1.4" fill="#FFFFFF" />
                    <circle cx="31.5" cy="11" r="1.1" fill="#FFFFFF" />
                    <path
                      d="M 16 14.5 h 8.5 c 7.2 0 12.2 4 12.2 12.2 c 0 8.2 -5 12.2 -12.2 12.2 h -8.5 z m 4.8 20.2 h 3.5 c 4.6 0 7.5 -2.4 7.5 -7.8 c 0 -5.4 -2.9 -7.8 -7.5 -7.8 h -3.5 z"
                      fill="#FFFFFF"
                    />
                    <path
                      d="M 24.5 25.5 C 26.5 22, 31.5 22, 32 26 C 32.5 30.5, 26.5 31.5, 23 34 C 27 35.8, 31 38.5, 27.5 41.5 C 24 43.5, 21 39.5, 24 35.5"
                      fill="none"
                      stroke="#FFFFFF"
                      stroke-width="1.6"
                      stroke-linecap="round"
                      stroke-linejoin="round"
                    />
                  </svg>
                </div>
                <div class="schedule-brand-names">
                  <span class="schedule-brand-main">DermoStetica</span>
                  <span class="schedule-brand-sub">MEDICINA ESTÉTICA Y LÁSER</span>
                </div>
              </div>
              <div class="schedule-loc-pill">
                <span>📍 @ DermoSTÉTICA</span>
              </div>
            </div>

            <h3 class="schedule-main-heading">
              Horarios de<br /><strong>atención</strong>
            </h3>

            <div class="schedule-time-boxes">
              <div class="time-box">
                <span class="time-box-pill">Lunes a Viernes</span>
                <strong class="time-box-hours">09H00 a 19H00</strong>
              </div>
              <div class="time-box">
                <span class="time-box-pill">Sábados</span>
                <strong class="time-box-hours">09H00 a 16H00</strong>
              </div>
            </div>

            <div class="schedule-booking-callout">
              <span class="booking-label">Reserva ahora:</span>
              <a
                :href="getWhatsAppUrl('Hola DermoSTETICA, deseo agendar una cita en su sede de Salinas.', whatsappNumber2)"
                target="_blank"
                class="booking-phone"
              >
                095 983 2254
              </a>
            </div>

            <div class="schedule-clock-graphic" aria-hidden="true">
              <svg viewBox="0 0 100 100" class="alarm-clock-svg">
                <circle cx="28" cy="24" r="12" fill="#FFC933" stroke="#D48B00" stroke-width="2"/>
                <circle cx="72" cy="24" r="12" fill="#FFC933" stroke="#D48B00" stroke-width="2"/>
                <line x1="26" y1="84" x2="16" y2="94" stroke="#D48B00" stroke-width="5" stroke-linecap="round"/>
                <line x1="74" y1="84" x2="84" y2="94" stroke="#D48B00" stroke-width="5" stroke-linecap="round"/>
                <rect x="47" y="10" width="6" height="8" rx="2" fill="#D48B00"/>
                <circle cx="50" cy="54" r="34" fill="#FFC933" stroke="#E59000" stroke-width="3"/>
                <circle cx="50" cy="54" r="27" fill="#FFFFFF"/>
                <circle cx="50" cy="31" r="2" fill="#333333"/>
                <circle cx="73" cy="54" r="2" fill="#333333"/>
                <circle cx="50" cy="77" r="2" fill="#333333"/>
                <circle cx="27" cy="54" r="2" fill="#333333"/>
                <line x1="50" y1="54" x2="50" y2="36" stroke="#222222" stroke-width="3" stroke-linecap="round"/>
                <line x1="50" y1="54" x2="35" y2="54" stroke="#222222" stroke-width="3.5" stroke-linecap="round"/>
                <circle cx="50" cy="54" r="3.5" fill="#FA9E00"/>
              </svg>
            </div>

            <a
              class="button-white-reserve"
              :href="getWhatsAppUrl('Hola DermoSTETICA, deseo consultar disponibilidad y reservar cita previa.', whatsappNumber2)"
              target="_blank"
              rel="noreferrer"
            >
              <span>Agendar por WhatsApp (095 983 2254)</span>
              <span aria-hidden="true">↗</span>
            </a>
          </div>
        </div>

        <!-- Layout 2: Official 11th Anniversary Packages (matching user images) -->
        <div v-else-if="activeStory.type === 'anniversary'" class="story-anniversary-body">
          <div class="anniversary-story-header">
            <div class="anniversary-badge-row">
              <span class="anniversary-pill">✦ 11 ANIVERSARIO ✦</span>
              <span class="anniversary-validity">Válido todo el mes</span>
            </div>
            <h3 class="anniversary-headline">
              Celebramos nuestros años <em>acompañándote</em>
            </h3>
            <p class="anniversary-subhead">
              11 años · 11 tratamientos más elegidos con beneficios y regalos especiales.
            </p>
          </div>

          <div class="anniversary-packages-list">
            <article
              v-for="pkg in activeStory.packages"
              :key="pkg.name"
              class="anniversary-card-item"
            >
              <div class="anniversary-card-top">
                <span class="anniversary-pkg-name">{{ pkg.name }}</span>
                <div class="anniversary-price-tag">
                  <span class="pkg-price-num">{{ pkg.price }}</span>
                  <small class="pkg-sessions-tag">{{ pkg.sessions }}</small>
                </div>
              </div>
              <p class="anniversary-pkg-desc">{{ pkg.subtitle }}</p>

              <div v-if="pkg.gift" class="anniversary-gift-pill">
                <span class="gift-icon">🎁</span>
                <span>{{ pkg.gift }}</span>
              </div>
              <div v-else-if="pkg.included" class="anniversary-included-pill">
                <span class="gift-icon">✦</span>
                <span>{{ pkg.included }}</span>
              </div>
              <div v-else-if="pkg.tag" class="anniversary-special-tag">
                <span>★ {{ pkg.tag }}</span>
              </div>

              <a
                class="anniversary-book-btn"
                :href="getWhatsAppUrl(`Hola Dra. Evelyn, deseo reservar la promoción de 11 Aniversario: ${pkg.name} (${pkg.price}).`, whatsappNumber2)"
                target="_blank"
                rel="noreferrer"
              >
                <span>Reservar este paquete</span>
                <span>↗</span>
              </a>
            </article>
          </div>
        </div>

        <!-- Layout 3: Standard Treatments Story Body -->
        <div v-else class="story-modal-body">
          <div class="story-modal-img">
            <img :src="activeStory.image" :alt="activeStory.title" />
            <div class="story-img-fade"></div>
          </div>
          <div class="story-modal-info">
            <span class="story-modal-tag">{{ activeStory.subtitle }}</span>
            <h4>{{ activeStory.title }}</h4>
            <p>{{ activeStory.content }}</p>
            <div class="story-modal-meta">
              <span>📍 {{ activeStory.location }}</span>
              <span v-if="activeStory.hours">⏰ {{ activeStory.hours }}</span>
            </div>
            <a
              class="button button-gold-bright story-modal-btn"
              :href="getWhatsAppUrl(`Hola Dra. Evelyn, estuve viendo su historia destacada sobre ${activeStory.title} y deseo consultar disponibilidad.`)"
              target="_blank"
              rel="noreferrer"
            >
              Consultar sobre {{ activeStory.title }} ↗
            </a>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
