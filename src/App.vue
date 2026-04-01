<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted, watch } from 'vue'

interface GameState {
  bx: number; be: number; bl: number; by: number; bz: number; ba: number
  bxUpgrade1: number; bxUpgrade2: number; bxUpgrade3: number; bxUpgrade4: number
  byUpgrade1: number; byUpgrade2: number; byUpgrade3: number; byUpgrade4: number
  bzUpgrade1: number; bzUpgrade2: number; bzUpgrade3: number; bzUpgrade4: number
  baUpgrade1: number; baUpgrade2: number; baUpgrade3: number; baUpgrade4: number
  bc: number; bd: number; bf: number; bg: number; bh: number; bi: number
  bcUpgrade1: number; bcUpgrade2: number; bcUpgrade3: number; bcUpgrade4: number
  bgUpgrade1: number; bgUpgrade2: number; bgUpgrade3: number; bgUpgrade4: number
  bhUpgrade1: number; bhUpgrade2: number; bhUpgrade3: number; bhUpgrade4: number
  biUpgrade1: number; biUpgrade2: number; biUpgrade3: number; biUpgrade4: number
  bj: number; bk: number; bm: number; bn: number; bo: number; bp: number
  bjUpgrade1: number; bjUpgrade2: number; bjUpgrade3: number; bjUpgrade4: number
  bnUpgrade1: number; bnUpgrade2: number; bnUpgrade3: number; bnUpgrade4: number
  boUpgrade1: number; boUpgrade2: number; boUpgrade3: number; boUpgrade4: number
  bpUpgrade1: number; bpUpgrade2: number; bpUpgrade3: number; bpUpgrade4: number
  bq: number; br: number; bs: number; bt: number; bu: number; bv: number
  bqUpgrade1: number; bqUpgrade2: number; bqUpgrade3: number; bqUpgrade4: number
  btUpgrade1: number; btUpgrade2: number; btUpgrade3: number; btUpgrade4: number
  buUpgrade1: number; buUpgrade2: number; buUpgrade3: number; buUpgrade4: number
  bvUpgrade1: number; bvUpgrade2: number; bvUpgrade3: number; bvUpgrade4: number
  bw: number
  bxAuto1Unlocked: boolean; bxAuto2Unlocked: boolean; bxAuto3Unlocked: boolean; bxAuto4Unlocked: boolean
  bxAuto5Unlocked: boolean; bxAuto6Unlocked: boolean; bxAuto7Unlocked: boolean; bxAuto8Unlocked: boolean
  bxAuto1Level: number; bxAuto2Level: number; bxAuto3Level: number; bxAuto4Level: number
  bxAuto5Level: number; bxAuto6Level: number; bxAuto7Level: number; bxAuto8Level: number
  bcAuto1Unlocked: boolean; bcAuto2Unlocked: boolean; bcAuto3Unlocked: boolean; bcAuto4Unlocked: boolean
  bcAuto5Unlocked: boolean; bcAuto6Unlocked: boolean; bcAuto7Unlocked: boolean; bcAuto8Unlocked: boolean
  bcAuto1Level: number; bcAuto2Level: number; bcAuto3Level: number; bcAuto4Level: number
  bcAuto5Level: number; bcAuto6Level: number; bcAuto7Level: number; bcAuto8Level: number
  bjAuto1Unlocked: boolean; bjAuto2Unlocked: boolean; bjAuto3Unlocked: boolean; bjAuto4Unlocked: boolean
  bjAuto5Unlocked: boolean; bjAuto6Unlocked: boolean; bjAuto7Unlocked: boolean; bjAuto8Unlocked: boolean
  bjAuto1Level: number; bjAuto2Level: number; bjAuto3Level: number; bjAuto4Level: number
  bjAuto5Level: number; bjAuto6Level: number; bjAuto7Level: number; bjAuto8Level: number
  bqAuto1Unlocked: boolean; bqAuto2Unlocked: boolean; bqAuto3Unlocked: boolean; bqAuto4Unlocked: boolean
  bqAuto5Unlocked: boolean; bqAuto6Unlocked: boolean; bqAuto7Unlocked: boolean; bqAuto8Unlocked: boolean
  bqAuto1Level: number; bqAuto2Level: number; bqAuto3Level: number; bqAuto4Level: number
  bqAuto5Level: number; bqAuto6Level: number; bqAuto7Level: number; bqAuto8Level: number
  gameEnded: boolean
}

const defaultSave: GameState = {
  bx: 0, be: 0, bl: 1, by: 0, bz: 0, ba: 0,
  bxUpgrade1: 0, bxUpgrade2: 0, bxUpgrade3: 0, bxUpgrade4: 0,
  byUpgrade1: 0, byUpgrade2: 0, byUpgrade3: 0, byUpgrade4: 0,
  bzUpgrade1: 0, bzUpgrade2: 0, bzUpgrade3: 0, bzUpgrade4: 0,
  baUpgrade1: 0, baUpgrade2: 0, baUpgrade3: 0, baUpgrade4: 0,
  bc: 0, bd: 0, bf: 1, bg: 0, bh: 0, bi: 0,
  bcUpgrade1: 0, bcUpgrade2: 0, bcUpgrade3: 0, bcUpgrade4: 0,
  bgUpgrade1: 0, bgUpgrade2: 0, bgUpgrade3: 0, bgUpgrade4: 0,
  bhUpgrade1: 0, bhUpgrade2: 0, bhUpgrade3: 0, bhUpgrade4: 0,
  biUpgrade1: 0, biUpgrade2: 0, biUpgrade3: 0, biUpgrade4: 0,
  bj: 0, bk: 0, bm: 1, bn: 0, bo: 0, bp: 0,
  bjUpgrade1: 0, bjUpgrade2: 0, bjUpgrade3: 0, bjUpgrade4: 0,
  bnUpgrade1: 0, bnUpgrade2: 0, bnUpgrade3: 0, bnUpgrade4: 0,
  boUpgrade1: 0, boUpgrade2: 0, boUpgrade3: 0, boUpgrade4: 0,
  bpUpgrade1: 0, bpUpgrade2: 0, bpUpgrade3: 0, bpUpgrade4: 0,
  bq: 0, br: 0, bs: 1, bt: 0, bu: 0, bv: 0,
  bqUpgrade1: 0, bqUpgrade2: 0, bqUpgrade3: 0, bqUpgrade4: 0,
  btUpgrade1: 0, btUpgrade2: 0, btUpgrade3: 0, btUpgrade4: 0,
  buUpgrade1: 0, buUpgrade2: 0, buUpgrade3: 0, buUpgrade4: 0,
  bvUpgrade1: 0, bvUpgrade2: 0, bvUpgrade3: 0, bvUpgrade4: 0,
  bw: 0,
  bxAuto1Unlocked: false, bxAuto2Unlocked: false, bxAuto3Unlocked: false, bxAuto4Unlocked: false,
  bxAuto5Unlocked: false, bxAuto6Unlocked: false, bxAuto7Unlocked: false, bxAuto8Unlocked: false,
  bxAuto1Level: 0, bxAuto2Level: 0, bxAuto3Level: 0, bxAuto4Level: 0,
  bxAuto5Level: 0, bxAuto6Level: 0, bxAuto7Level: 0, bxAuto8Level: 0,
  bcAuto1Unlocked: false, bcAuto2Unlocked: false, bcAuto3Unlocked: false, bcAuto4Unlocked: false,
  bcAuto5Unlocked: false, bcAuto6Unlocked: false, bcAuto7Unlocked: false, bcAuto8Unlocked: false,
  bcAuto1Level: 0, bcAuto2Level: 0, bcAuto3Level: 0, bcAuto4Level: 0,
  bcAuto5Level: 0, bcAuto6Level: 0, bcAuto7Level: 0, bcAuto8Level: 0,
  bjAuto1Unlocked: false, bjAuto2Unlocked: false, bjAuto3Unlocked: false, bjAuto4Unlocked: false,
  bjAuto5Unlocked: false, bjAuto6Unlocked: false, bjAuto7Unlocked: false, bjAuto8Unlocked: false,
  bjAuto1Level: 0, bjAuto2Level: 0, bjAuto3Level: 0, bjAuto4Level: 0,
  bjAuto5Level: 0, bjAuto6Level: 0, bjAuto7Level: 0, bjAuto8Level: 0,
  bqAuto1Unlocked: false, bqAuto2Unlocked: false, bqAuto3Unlocked: false, bqAuto4Unlocked: false,
  bqAuto5Unlocked: false, bqAuto6Unlocked: false, bqAuto7Unlocked: false, bqAuto8Unlocked: false,
  bqAuto1Level: 0, bqAuto2Level: 0, bqAuto3Level: 0, bqAuto4Level: 0,
  bqAuto5Level: 0, bqAuto6Level: 0, bqAuto7Level: 0, bqAuto8Level: 0,
  gameEnded: false
}

const game = ref<GameState>({ ...defaultSave })

const formatNumber = (num: number): string => {
  if (num === 0) return '0'
  if (num < 1000) return num.toFixed(1)
  const exp = Math.floor(Math.log10(num))
  const mantissa = num / Math.pow(10, exp)
  return `${mantissa.toFixed(3)}e${exp}`
}

// 升级花费计算：升级 1-3 乘数 1.5，升级 4 乘数 10
const getUpgradeCost = (level: number, baseCost: number, upgradeType: number): number => {
  const multiplier = upgradeType === 4 ? 10 : 1.25
  return Math.floor(baseCost * Math.pow(multiplier, level))
}

const getBlUpgradeCost = (bl: number): number => bl === 0 ? 1 : 50 * bl * Math.pow(1.2, bl)

// 升级效果计算：升级 1-2 每 10 级×2 每 100 级×10，升级 3 每 10 级×1.2 每 100 级×2
const getUpgrade1Or2Effect = (level: number): number => {
  let effect = 1 + level
  const hundreds = Math.floor(level / 100)
  const tens = Math.floor(level / 10)
  effect *= Math.pow(10, hundreds)
  effect *= Math.pow(2, tens)
  return effect
}

const getUpgrade3Effect = (level: number): number => {
  let effect = 1 + level * 0.1
  const hundreds = Math.floor(level / 100)
  const tens = Math.floor(level / 10)
  effect *= Math.pow(2, hundreds)
  effect *= Math.pow(1.2, tens)
  return effect
}

const getUpgrade4Effect = (level: number): number => 1 + Math.min(level, 9)
const getBlBonus = (): number => game.value.bl === 0 ? 1 : Math.floor(game.value.bl * Math.pow(1.05, game.value.bl))
const getByResetReward = (): number => game.value.bl < 40 ? 0 : 1 * Math.pow(1.04, game.value.bl - 40) * getUpgrade3Effect(game.value.bxUpgrade3)
const getBzResetReward = (): number => game.value.bl < 120 ? 0 : 1 * Math.pow(1.03, game.value.bl - 120) * getUpgrade3Effect(game.value.byUpgrade3)
const getBaResetReward = (): number => game.value.bl < 240 ? 0 : 1 * Math.pow(1.02, game.value.bl - 240) * getUpgrade3Effect(game.value.bzUpgrade3)

const isBcUnlocked = computed(() => game.value.bl >= 400)
//const isBjUnlocked = computed(() => game.value.bf >= 400)
//const isBqUnlocked = computed(() => game.value.bm >= 400)
const isBwUnlocked = computed(() => game.value.bs >= 400)
const getBwBonus = (): number => game.value.bw + 1

const autoUnlocked = (i: number): boolean => {
  const key = `bxAuto${i}Unlocked` as keyof GameState
  return game.value[key] as boolean
}

const autoLevel = (i: number): number => {
  const key = `bxAuto${i}Level` as keyof GameState
  return game.value[key] as number
}

const clickBx = () => {
  if (game.value.gameEnded) return
  const bxMultiplier = getUpgrade1Or2Effect(game.value.bxUpgrade1) * getUpgrade1Or2Effect(game.value.byUpgrade1) * getUpgrade1Or2Effect(game.value.bzUpgrade1) * getUpgrade1Or2Effect(game.value.baUpgrade1) * getBlBonus() * getBwBonus()
  const beMultiplier = getUpgrade1Or2Effect(game.value.bxUpgrade2) * getUpgrade1Or2Effect(game.value.byUpgrade2) * getUpgrade1Or2Effect(game.value.bzUpgrade2) * getUpgrade1Or2Effect(game.value.baUpgrade2) * getBwBonus()
  game.value.bx += 1 * bxMultiplier
  game.value.be += 1 * beMultiplier
}

const upgradeBx = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = 0
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bxUpgrade1, 10, 1)
      if (game.value.bx >= cost) { game.value.bx -= cost; game.value.bxUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bxUpgrade2, 100, 2)
      if (game.value.bx >= cost) { game.value.bx -= cost; game.value.bxUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bxUpgrade3, 1000, 3)
      if (game.value.bx >= cost) { game.value.bx -= cost; game.value.bxUpgrade3++ }
      break
    case 4:
      if (game.value.bxUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bxUpgrade4, 10000, 4)
        if (game.value.bx >= cost) { game.value.bx -= cost; game.value.bxUpgrade4++ }
      }
      break
  }
}

const upgradeBy = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = 0
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.byUpgrade1, 10, 1)
      if (game.value.by >= cost) { game.value.by -= cost; game.value.byUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.byUpgrade2, 100, 2)
      if (game.value.by >= cost) { game.value.by -= cost; game.value.byUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.byUpgrade3, 1000, 3)
      if (game.value.by >= cost) { game.value.by -= cost; game.value.byUpgrade3++ }
      break
    case 4:
      if (game.value.byUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.byUpgrade4, 10000, 4)
        if (game.value.by >= cost) { game.value.by -= cost; game.value.byUpgrade4++ }
      }
      break
  }
}

const upgradeBz = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = 0
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bzUpgrade1, 10, 1)
      if (game.value.bz >= cost) { game.value.bz -= cost; game.value.bzUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bzUpgrade2, 100, 2)
      if (game.value.bz >= cost) { game.value.bz -= cost; game.value.bzUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bzUpgrade3, 1000, 3)
      if (game.value.bz >= cost) { game.value.bz -= cost; game.value.bzUpgrade3++ }
      break
    case 4:
      if (game.value.bzUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bzUpgrade4, 10000, 4)
        if (game.value.bz >= cost) { game.value.bz -= cost; game.value.bzUpgrade4++ }
      }
      break
  }
}

const upgradeBa = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = 0
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.baUpgrade1, 10, 1)
      if (game.value.ba >= cost) { game.value.ba -= cost; game.value.baUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.baUpgrade2, 100, 2)
      if (game.value.ba >= cost) { game.value.ba -= cost; game.value.baUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.baUpgrade3, 1000, 3)
      if (game.value.ba >= cost) { game.value.ba -= cost; game.value.baUpgrade3++ }
      break
    case 4:
      if (game.value.baUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.baUpgrade4, 10000, 4)
        if (game.value.ba >= cost) { game.value.ba -= cost; game.value.baUpgrade4++ }
      }
      break
  }
}

const autoUpgradeBl = () => {
  if (game.value.gameEnded) return
  const cost = getBlUpgradeCost(game.value.bl)
  if (game.value.be >= cost && cost > 0) {
    game.value.be -= cost
    game.value.bl++
  }
}

const resetBy = () => {
  if (game.value.bl < 40) return
  game.value.by += getByResetReward()
  game.value.bx = 0; game.value.be = 0; game.value.bl = 1
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
}

const resetBz = () => {
  if (game.value.bl < 120) return
  game.value.bz += getBzResetReward()
  game.value.bx = 0; game.value.be = 0; game.value.bl = 1; game.value.by = 0
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
}

const resetBa = () => {
  if (game.value.bl < 240) return
  game.value.ba += getBaResetReward()
  game.value.bx = 0; game.value.be = 0; game.value.bl = 1; game.value.by = 0; game.value.bz = 0
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
}

// 拜谢层级自动化
const upgradeBxAuto = (autoIndex: number) => {
  if (game.value.gameEnded) return
  const autoKey = `bxAuto${autoIndex}Unlocked` as keyof GameState
  const levelKey = `bxAuto${autoIndex}Level` as keyof GameState
  const isUnlocked = game.value[autoKey] as unknown as boolean
  const level = game.value[levelKey] as unknown as number
  const unlockCosts = [1e4, 1e12, 1e12, 1e12, 1e8, 1e8, 1e8, 1e8]
  const unlockResources = [game.value.bx, game.value.by, game.value.bz, game.value.ba, game.value.bx, game.value.by, game.value.bz, game.value.ba]
  if (!isUnlocked) {
    if (unlockResources[autoIndex - 1] >= unlockCosts[autoIndex - 1]) {
      ;(game.value[autoKey] as unknown as boolean) = true
    }
  } else {
    const cost = Math.floor(1e6 * Math.pow(1.2, level))
    if (game.value.bx >= cost) {
      game.value.bx -= cost
      ;(game.value[levelKey] as unknown as number) = level + 1
    }
  }
}

const autoClickBx = () => {
  if (!game.value.bxAuto1Unlocked) return
  const clicks = 1 + game.value.bxAuto1Level
  for (let i = 0; i < clicks; i++) clickBx()
}

const autoGetResetResources = () => {
  if (game.value.bxAuto2Unlocked) {
    const rate = 1 + game.value.bxAuto2Level
    game.value.by += rate * getByResetReward() * getBwBonus()
  }
  if (game.value.bxAuto3Unlocked) {
    const rate = 1 + game.value.bxAuto3Level
    game.value.bz += rate * getBzResetReward() * getBwBonus()
  }
  if (game.value.bxAuto4Unlocked) {
    const rate = 1 + game.value.bxAuto4Level
    game.value.ba += rate * getBaResetReward() * getBwBonus()
  }
}

const calculateMaxPurchases = (resource: number, baseCost: number, multiplier: number, currentLevel: number): number => {
  if (resource < baseCost * Math.pow(multiplier, currentLevel)) return 0
  const r = multiplier
  for (let i = 1; i <= 1000; i++) {
    const totalCost = baseCost * Math.pow(r, currentLevel) * (Math.pow(r, i) - 1) / (r - 1)
    if (totalCost > resource) return i - 1
  }
  return 1000
}

const autoBuyUpgrades = () => {
  // 自动化 5：自动购买 BX 升级
  if (game.value.bxAuto5Unlocked) {
    const upgrades = [
      { level: game.value.bxUpgrade1, base: 10 },
      { level: game.value.bxUpgrade2, base: 100 },
      { level: game.value.bxUpgrade3, base: 1000 },
      { level: game.value.bxUpgrade4, base: 10000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bx, upgrade.base, 1.25, upgrade.level)
      if (maxBuy > 0) {
        const totalCost = upgrade.base * Math.pow(1.25, upgrade.level) * (Math.pow(1.25, maxBuy) - 1) / (1.25 - 1)
        game.value.bx -= totalCost
        switch (index) {
          case 0: game.value.bxUpgrade1 += maxBuy; break
          case 1: game.value.bxUpgrade2 += maxBuy; break
          case 2: game.value.bxUpgrade3 += maxBuy; break
          case 3: game.value.bxUpgrade4 = Math.min(game.value.bxUpgrade4 + maxBuy, 9); break
        }
      }
    })
  }
  // 自动化 6：自动购买 BY 升级
  if (game.value.bxAuto6Unlocked) {
    const upgrades = [
      { level: game.value.byUpgrade1, base: 10 },
      { level: game.value.byUpgrade2, base: 100 },
      { level: game.value.byUpgrade3, base: 1000 },
      { level: game.value.byUpgrade4, base: 10000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.by, upgrade.base, 1.25, upgrade.level)
      if (maxBuy > 0) {
        const totalCost = upgrade.base * Math.pow(1.25, upgrade.level) * (Math.pow(1.25, maxBuy) - 1) / (1.25 - 1)
        game.value.by -= totalCost
        switch (index) {
          case 0: game.value.byUpgrade1 += maxBuy; break
          case 1: game.value.byUpgrade2 += maxBuy; break
          case 2: game.value.byUpgrade3 += maxBuy; break
          case 3: game.value.byUpgrade4 = Math.min(game.value.byUpgrade4 + maxBuy, 9); break
        }
      }
    })
  }
  // 自动化 7：自动购买 BZ 升级
  if (game.value.bxAuto7Unlocked) {
    const upgrades = [
      { level: game.value.bzUpgrade1, base: 10 },
      { level: game.value.bzUpgrade2, base: 100 },
      { level: game.value.bzUpgrade3, base: 1000 },
      { level: game.value.bzUpgrade4, base: 10000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bz, upgrade.base, 1.25, upgrade.level)
      if (maxBuy > 0) {
        const totalCost = upgrade.base * Math.pow(1.25, upgrade.level) * (Math.pow(1.25, maxBuy) - 1) / (1.25 - 1)
        game.value.bz -= totalCost
        switch (index) {
          case 0: game.value.bzUpgrade1 += maxBuy; break
          case 1: game.value.bzUpgrade2 += maxBuy; break
          case 2: game.value.bzUpgrade3 += maxBuy; break
          case 3: game.value.bzUpgrade4 = Math.min(game.value.bzUpgrade4 + maxBuy, 9); break
        }
      }
    })
  }
  // 自动化 8：自动购买 BA 升级
  if (game.value.bxAuto8Unlocked) {
    const upgrades = [
      { level: game.value.baUpgrade1, base: 10 },
      { level: game.value.baUpgrade2, base: 100 },
      { level: game.value.baUpgrade3, base: 1000 },
      { level: game.value.baUpgrade4, base: 10000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.ba, upgrade.base, 1.25, upgrade.level)
      if (maxBuy > 0) {
        const totalCost = upgrade.base * Math.pow(1.25, upgrade.level) * (Math.pow(1.25, maxBuy) - 1) / (1.25 - 1)
        game.value.ba -= totalCost
        switch (index) {
          case 0: game.value.baUpgrade1 += maxBuy; break
          case 1: game.value.baUpgrade2 += maxBuy; break
          case 2: game.value.baUpgrade3 += maxBuy; break
          case 3: game.value.baUpgrade4 = Math.min(game.value.baUpgrade4 + maxBuy, 9); break
        }
      }
    })
  }
}

let gameLoop: number, saveInterval: number, animationFrame: number

interface BgDecoration { id: number; x: number; y: number; size: number; rotation: number; speed: number; opacity: number }
const bgDecorations = ref<BgDecoration[]>([])

const startGameLoop = () => {
  gameLoop = window.setInterval(() => {
    if (game.value.bxAuto1Unlocked) autoClickBx()
    autoGetResetResources()
    autoBuyUpgrades()
    autoUpgradeBl()
    if (game.value.bw >= 114514) { game.value.bw = 114514; game.value.gameEnded = true }
  }, 1000)
  saveInterval = window.setInterval(() => saveGame(), 5000)
  const animate = () => {
    bgDecorations.value.forEach(deco => {
      deco.rotation += deco.speed
      deco.x += Math.sin(deco.rotation * 0.01) * 0.01
      deco.y += Math.cos(deco.rotation * 0.01) * 0.01
    })
    animationFrame = requestAnimationFrame(animate)
  }
  animate()
}

const saveGame = () => localStorage.setItem('baixie_save', JSON.stringify(game.value))
const loadGame = () => {
  const saved = localStorage.getItem('baixie_save')
  if (saved) {
    try { game.value = { ...defaultSave, ...JSON.parse(saved) } } catch (e) { console.error('Failed to load save', e) }
  }
}
const exportSave = () => {
  const data = JSON.stringify(game.value)
  const blob = new Blob([data], { type: 'application/json' })
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a')
  a.href = url
  a.download = `baixie_save_${new Date().toISOString()}.json`
  a.click()
  URL.revokeObjectURL(url)
}
const importSave = (event: Event) => {
  const target = event.target as HTMLInputElement
  const file = target.files?.[0]
  if (!file) return
  const reader = new FileReader()
  reader.onload = (e) => {
    try {
      game.value = { ...defaultSave, ...JSON.parse(e.target?.result as string) }
      saveGame()
      alert('存档导入成功！')
    } catch (err) { alert('存档导入失败！') }
  }
  reader.readAsText(file)
  target.value = ''
}
const hardReset = () => {
  if (confirm('确定要删除所有存档数据吗？此操作不可恢复！')) {
    game.value = { ...defaultSave }
    localStorage.removeItem('baixie_save')
  }
}
const resetGod = () => {
  if (!isBwUnlocked.value) return
  game.value.bw += 3 * Math.pow(1.01, game.value.bs - 400)
  game.value.bx = 0; game.value.be = 0; game.value.bl = 1; game.value.by = 0; game.value.bz = 0; game.value.ba = 0
  game.value.bc = 0; game.value.bd = 0; game.value.bf = 1; game.value.bg = 0; game.value.bh = 0; game.value.bi = 0
  game.value.bj = 0; game.value.bk = 0; game.value.bm = 1; game.value.bn = 0; game.value.bo = 0; game.value.bp = 0
  game.value.bq = 0; game.value.br = 0; game.value.bs = 1; game.value.bt = 0; game.value.bu = 0; game.value.bv = 0
  Object.keys(game.value).forEach(key => { if (key.includes('Upgrade')) (game.value[key as keyof GameState] as unknown as number) = 0 })
  game.value.bxAuto1Unlocked = false; game.value.bxAuto2Unlocked = false; game.value.bxAuto3Unlocked = false; game.value.bxAuto4Unlocked = false
  game.value.bxAuto5Unlocked = false; game.value.bxAuto6Unlocked = false; game.value.bxAuto7Unlocked = false; game.value.bxAuto8Unlocked = false
  game.value.bxAuto1Level = 0; game.value.bxAuto2Level = 0; game.value.bxAuto3Level = 0; game.value.bxAuto4Level = 0
  game.value.bxAuto5Level = 0; game.value.bxAuto6Level = 0; game.value.bxAuto7Level = 0; game.value.bxAuto8Level = 0
}

onMounted(() => {
  loadGame()
  startGameLoop()
  for (let i = 0; i < 100; i++) {
    bgDecorations.value.push({
      id: i, x: Math.random() * 100, y: Math.random() * 100, size: 30 + Math.random() * 200,
      rotation: Math.random() * 360, speed: (Math.random() - 0.5) * 0.5, opacity: 0.3 + Math.random() * 0.3
    })
  }
})
onUnmounted(() => { clearInterval(gameLoop); clearInterval(saveInterval); cancelAnimationFrame(animationFrame) })
watch(game, () => saveGame(), { deep: true })
</script>

<template>
  <div class="game-container">
    <div class="bg-decorations">
      <div v-for="deco in bgDecorations" :key="deco.id" class="bg-decoration"
        :style="{ left: deco.x + '%', top: deco.y + '%', width: deco.size + 'px', height: deco.size + 'px',
          transform: `rotate(${deco.rotation}deg)`, opacity: deco.opacity }">
        <img src="/baixie.png" alt="" />
      </div>
    </div>
    <div class="game-content">
      <h1>拜谢增量拜谢版</h1>
      <div v-if="game.gameEnded" class="ending-message">
        <h2>🎉 恭喜通关！你已到达游戏结局 🎉</h2>
        <div class="ending-bg">
          <img src="/baixie.png" alt="" class="ending-img" v-for="i in 5" :key="i" />
        </div>
      </div>
      <div class="section">
        <h2>拜谢层级</h2>
        <div class="resources">
          <div class="resource">拜谢：<span class="counter">{{ formatNumber(game.bx) }}</span></div>
          <div class="resource">拜谢经验：<span class="counter">{{ formatNumber(game.be) }}</span></div>
          <div class="resource">拜谢等级：<span class="counter">{{ game.bl }}</span> <span class="bonus">加成：×{{ formatNumber(getBlBonus()) }}</span></div>
          <div class="resource">拝谣：<span class="counter">{{ formatNumber(game.by) }}</span></div>
          <div class="resource">拞谤：<span class="counter">{{ formatNumber(game.bz) }}</span></div>
          <div class="resource">拟谥：<span class="counter">{{ formatNumber(game.ba) }}</span></div>
        </div>
        <div class="actions">
          <button @click="clickBx" class="main-btn"><img src="/baixie.png" alt="" class="btn-icon" />拜谢一次</button>
        </div>
        <div class="progress-bar">
          <div class="progress-fill" :style="{ width: Math.min((game.be / getBlUpgradeCost(game.bl)) * 100, 100) + '%' }">
            <span class="progress-text">{{ formatNumber(game.be) }} / {{ formatNumber(getBlUpgradeCost(game.bl)) }}</span>
          </div>
        </div>
        <div class="reset-section" v-if="game.bl >= 40">
          <h3>重置系统</h3>
          <button @click="resetBy" class="reset-btn" :disabled="game.bl < 40">
            <img src="/baixie.png" alt="" class="btn-icon" />拝谣重置 (获得 {{ formatNumber(getByResetReward()) }} BY)
          </button>
        </div>
        <div class="reset-section" v-if="game.bl >= 120">
          <button @click="resetBz" class="reset-btn" :disabled="game.bl < 120">
            <img src="/baixie.png" alt="" class="btn-icon" />拞谤重置 (获得 {{ formatNumber(getBzResetReward()) }} BZ)
          </button>
        </div>
        <div class="reset-section" v-if="game.bl >= 240">
          <button @click="resetBa" class="reset-btn" :disabled="game.bl < 240">
            <img src="/baixie.png" alt="" class="btn-icon" />拟谥重置 (获得 {{ formatNumber(getBaResetReward()) }} BA)
          </button>
        </div>
        <div class="upgrades">
          <h3>拜谢升级</h3>
          <button @click="upgradeBx(1)" :disabled="game.bx < getUpgradeCost(game.bxUpgrade1, 10, 1)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: bx 获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bxUpgrade1)) }} (Lv.{{ game.bxUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bxUpgrade1, 10, 1)) }} BX
          </button>
          <button @click="upgradeBx(2)" :disabled="game.bx < getUpgradeCost(game.bxUpgrade2, 100, 2)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: be 获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bxUpgrade2)) }} (Lv.{{ game.bxUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bxUpgrade2, 100, 2)) }} BX
          </button>
          <button @click="upgradeBx(3)" :disabled="game.bx < getUpgradeCost(game.bxUpgrade3, 1000, 3)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: by 获取 ×{{ formatNumber(getUpgrade3Effect(game.bxUpgrade3)) }} (Lv.{{ game.bxUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bxUpgrade3, 1000, 3)) }} BX
          </button>
          <button @click="upgradeBx(4)" :disabled="game.bx < getUpgradeCost(game.bxUpgrade4, 10000, 4) || game.bxUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击 ×{{ formatNumber(getUpgrade4Effect(game.bxUpgrade4)) }} (Lv.{{ game.bxUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bxUpgrade4, 10000, 4)) }} BX
          </button>
        </div>
        <div class="upgrades" v-if="game.bl >= 40 || game.by > 0">
          <h3>拝谣升级</h3>
          <button @click="upgradeBy(1)" :disabled="game.by < getUpgradeCost(game.byUpgrade1, 10, 1)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: bx 获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.byUpgrade1)) }} (Lv.{{ game.byUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.byUpgrade1, 10, 1)) }} BY
          </button>
          <button @click="upgradeBy(2)" :disabled="game.by < getUpgradeCost(game.byUpgrade2, 100, 2)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: be 获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.byUpgrade2)) }} (Lv.{{ game.byUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.byUpgrade2, 100, 2)) }} BY
          </button>
          <button @click="upgradeBy(3)" :disabled="game.by < getUpgradeCost(game.byUpgrade3, 1000, 3)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: bz 获取 ×{{ formatNumber(getUpgrade3Effect(game.byUpgrade3)) }} (Lv.{{ game.byUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.byUpgrade3, 1000, 3)) }} BY
          </button>
          <button @click="upgradeBy(4)" :disabled="game.by < getUpgradeCost(game.byUpgrade4, 10000, 4) || game.byUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动获得 ×{{ formatNumber(getUpgrade4Effect(game.byUpgrade4)) }} (Lv.{{ game.byUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.byUpgrade4, 10000, 4)) }} BY
          </button>
        </div>
        <div class="upgrades" v-if="game.bl >= 120 || game.bz > 0">
          <h3>拞谤升级</h3>
          <button @click="upgradeBz(1)" :disabled="game.bz < getUpgradeCost(game.bzUpgrade1, 10, 1)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: bx 获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bzUpgrade1)) }} (Lv.{{ game.bzUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bzUpgrade1, 10, 1)) }} BZ
          </button>
          <button @click="upgradeBz(2)" :disabled="game.bz < getUpgradeCost(game.bzUpgrade2, 100, 2)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: be 获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bzUpgrade2)) }} (Lv.{{ game.bzUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bzUpgrade2, 100, 2)) }} BZ
          </button>
          <button @click="upgradeBz(3)" :disabled="game.bz < getUpgradeCost(game.bzUpgrade3, 1000, 3)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: ba 获取 ×{{ formatNumber(getUpgrade3Effect(game.bzUpgrade3)) }} (Lv.{{ game.bzUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bzUpgrade3, 1000, 3)) }} BZ
          </button>
          <button @click="upgradeBz(4)" :disabled="game.bz < getUpgradeCost(game.bzUpgrade4, 10000, 4) || game.bzUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动获得 ×{{ formatNumber(getUpgrade4Effect(game.bzUpgrade4)) }} (Lv.{{ game.bzUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bzUpgrade4, 10000, 4)) }} BZ
          </button>
        </div>
        <div class="upgrades" v-if="game.bl >= 240 || game.ba > 0">
          <h3>拟谥升级</h3>
          <button @click="upgradeBa(1)" :disabled="game.ba < getUpgradeCost(game.baUpgrade1, 10, 1)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: bx 获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.baUpgrade1)) }} (Lv.{{ game.baUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.baUpgrade1, 10, 1)) }} BA
          </button>
          <button @click="upgradeBa(2)" :disabled="game.ba < getUpgradeCost(game.baUpgrade2, 100, 2)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: be 获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.baUpgrade2)) }} (Lv.{{ game.baUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.baUpgrade2, 100, 2)) }} BA
          </button>
          <button @click="upgradeBa(3)" :disabled="game.ba < getUpgradeCost(game.baUpgrade3, 1000, 3)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: bc 获取 ×{{ formatNumber(getUpgrade3Effect(game.baUpgrade3)) }} (Lv.{{ game.baUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.baUpgrade3, 1000, 3)) }} BA
          </button>
          <button @click="upgradeBa(4)" :disabled="game.ba < getUpgradeCost(game.baUpgrade4, 10000, 4) || game.baUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动获得 ×{{ formatNumber(getUpgrade4Effect(game.baUpgrade4)) }} (Lv.{{ game.baUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.baUpgrade4, 10000, 4)) }} BA
          </button>
        </div>
      </div>
      <div class="section">
        <h2>拜谢自动化（8 种）</h2>
        <div class="auto-upgrades">
          <div v-for="i in 8" :key="i" class="auto-upgrade">
            <button @click="upgradeBxAuto(i)" :class="{ locked: !autoUnlocked(i) }" class="auto-btn">
              <img src="/baixie.png" alt="" class="btn-icon" />
              <div class="auto-title">{{ autoUnlocked(i) ? `自动化${i} Lv.${autoLevel(i)}` : `解锁自动化${i}` }}</div>
              <div class="auto-desc">
                <span v-if="i===1">效果：每秒自动获得 BX 和 BE</span>
                <span v-else-if="i===2">效果：每秒自动获得 BY</span>
                <span v-else-if="i===3">效果：每秒自动获得 BZ</span>
                <span v-else-if="i===4">效果：每秒自动获得 BA</span>
                <span v-else-if="i===5">效果：自动购买 BX 升级</span>
                <span v-else-if="i===6">效果：自动购买 BY 升级</span>
                <span v-else-if="i===7">效果：自动购买 BZ 升级</span>
                <span v-else>效果：自动购买 BA 升级</span>
              </div>
              <div class="auto-cost">
                花费：{{ formatNumber(autoUnlocked(i) ? Math.floor(1e6 * Math.pow(1.2, autoLevel(i))) : [1e4,1e12,1e12,1e12,1e8,1e8,1e8,1e8][i-1]) }} {{ ['BX','BY','BZ','BA','BX','BY','BZ','BA'][i-1] }}
              </div>
            </button>
          </div>
        </div>
      </div>
      <div class="section" v-if="isBcUnlocked">
        <h2>拠谦层级</h2>
        <div class="resources">
          <div class="resource">拠谦：<span class="counter">{{ formatNumber(game.bc) }}</span></div>
          <div class="resource">拠谦经验：<span class="counter">{{ formatNumber(game.bd) }}</span></div>
          <div class="resource">拠谦等级：<span class="counter">{{ game.bf }}</span> <span class="bonus">加成：×{{ formatNumber(getBlBonus()) }}</span></div>
        </div>
      </div>
      <div class="section god-section" v-if="isBwUnlocked">
        <h2>拜谢之神</h2>
        <div class="god-content">
          <img src="/baixie.png" alt="" class="god-img" />
          <div class="god-resources">
            <div class="resource">拜谢之神：<span class="counter">{{ formatNumber(game.bw) }}</span></div>
            <div class="resource">全局加成：×{{ formatNumber(getBwBonus()) }}</div>
          </div>
          <button @click="resetGod" class="god-reset-btn"><img src="/baixie.png" alt="" class="btn-icon" />拜谢之神重置</button>
        </div>
      </div>
      <div class="section save-section">
        <h2>存档系统</h2>
        <div class="save-buttons">
          <button @click="exportSave" class="save-btn"><img src="/baixie.png" alt="" class="btn-icon" />导出存档</button>
          <label class="save-btn"><img src="/baixie.png" alt="" class="btn-icon" />导入存档<input type="file" @change="importSave" accept=".json" style="display: none" /></label>
          <button @click="hardReset" class="reset-btn danger"><img src="/baixie.png" alt="" class="btn-icon" />硬重置</button>
          <a href="https://www.bilibili.com/video/BV1GJ411x7h7" target="_blank" class="save-btn completion-btn"><img src="/baixie.png" alt="" class="btn-icon" />通关这个游戏</a>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.game-container {
  position: relative;
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 20px;
  overflow: hidden;
}
.bg-decorations {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 0;
}
.bg-decoration {
  position: absolute;
  pointer-events: none;
  transition: left 0.1s linear, top 0.1s linear;
}
.bg-decoration img {
  width: 100%;
  height: 100%;
  object-fit: contain;
}
.game-content {
  position: relative;
  z-index: 1;
  max-width: 1200px;
  margin: 0 auto;
  background: transparent;
  border-radius: 15px;
  padding: 30px;
}
h1, h2, h3 {
  color: #000;
  text-align: center;
  margin-bottom: 20px;
}
h1 { font-size: 2.5em; }
h2 {
  border-bottom: 2px solid #ffd700;
  padding-bottom: 10px;
}
.section {
  margin-bottom: 30px;
  padding: 20px;
  background: rgba(255, 215, 0, 0.1);
  border-radius: 10px;
  border: 2px solid rgba(255, 215, 0, 0.5);
}
.resources {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 15px;
  margin-bottom: 20px;
}
.resource {
  padding: 10px;
  background: rgba(255, 215, 0, 0.2);
  border-radius: 5px;
  text-align: center;
  border: 1px solid rgba(255, 215, 0, 0.3);
  color: #000;
}
.counter {
  font-weight: bold;
  font-size: 1.2em;
  color: #000;
}
.bonus {
  display: block;
  font-size: 0.9em;
  color: #000;
  margin-top: 5px;
}
.actions {
  display: flex;
  gap: 15px;
  justify-content: center;
  flex-wrap: wrap;
  margin-bottom: 20px;
}
.main-btn, .upgrade-btn, .reset-btn, .save-btn, .auto-btn, .god-reset-btn {
  padding: 12px 25px;
  font-size: 16px;
  background: linear-gradient(135deg, #ffd700 0%, #ffaa00 100%);
  color: #000;
  border: 2px solid #ffd700;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s;
  display: flex;
  align-items: center;
  gap: 10px;
  font-weight: bold;
}
.main-btn:hover, .upgrade-btn:hover:not(:disabled), .reset-btn:hover:not(:disabled),
.save-btn:hover, .auto-btn:hover:not(.locked), .god-reset-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 20px rgba(255, 215, 0, 0.6);
}
.main-btn:active { transform: translateY(0); }
.upgrade-btn:disabled, .reset-btn:disabled, .auto-btn.locked {
  opacity: 0.5;
  cursor: not-allowed;
}
.btn-icon {
  width: 30px;
  height: 30px;
  object-fit: contain;
}
.progress-bar {
  width: 100%;
  height: 30px;
  background: rgba(255, 215, 0, 0.2);
  border-radius: 10px;
  overflow: hidden;
  margin: 15px 0;
  border: 2px solid rgba(255, 215, 0, 0.5);
}
.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #ffd700, #ffaa00);
  transition: width 0.3s;
  display: flex;
  align-items: center;
  justify-content: center;
}
.progress-text {
  color: #000;
  font-weight: bold;
  font-size: 0.9em;
  white-space: nowrap;
}
.upgrades {
  margin-top: 20px;
}
.upgrades h3 {
  color: #000;
  margin-bottom: 15px;
}
.upgrade-btn {
  width: 100%;
  margin-bottom: 10px;
  justify-content: flex-start;
}
.reset-section {
  margin-top: 20px;
  padding-top: 20px;
  border-top: 2px dashed rgba(255, 215, 0, 0.5);
}
.reset-btn {
  margin: 5px;
}
.reset-btn.danger {
  background: linear-gradient(135deg, #ff6600 0%, #ff4400 100%);
}
.completion-btn {
  background: linear-gradient(135deg, #00c6ff 0%, #0072ff 100%);
  color: #fff;
  text-decoration: none;
}
.completion-btn:hover {
  box-shadow: 0 5px 20px rgba(0, 114, 255, 0.6);
}
.auto-upgrades {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 15px;
}
.auto-btn {
  width: 100%;
  padding: 15px;
  flex-direction: column;
  gap: 8px;
  align-items: stretch;
}
.auto-title {
  font-weight: bold;
  color: #000;
}
.auto-desc {
  font-size: 13px;
  color: #000;
}
.auto-cost {
  font-size: 13px;
  color: #000;
  font-weight: bold;
}
.auto-btn.locked {
  background: rgba(128, 128, 128, 0.5);
  border-color: #888;
  color: #888;
}
.god-section {
  background: rgba(255, 215, 0, 0.15);
  border-color: #ffd700;
}
.god-content {
  text-align: center;
}
.god-img {
  width: 150px;
  height: 150px;
  margin-bottom: 20px;
  animation: float 3s ease-in-out infinite;
}
@keyframes float {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}
.god-reset-btn {
  padding: 20px 40px;
  font-size: 16px;
  margin-top: 20px;
  display: inline-flex;
}
.save-section {
  text-align: center;
}
.save-buttons {
  display: flex;
  gap: 15px;
  justify-content: center;
  flex-wrap: wrap;
}
.ending-message {
  background: linear-gradient(135deg, #ffd700 0%, #ffaa00 100%);
  color: #000;
  padding: 30px;
  border-radius: 15px;
  margin-bottom: 30px;
  text-align: center;
  border: 3px solid #fff;
}
.ending-bg {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-top: 20px;
}
.ending-img {
  width: 100px;
  height: 100px;
  animation: spin 5s linear infinite;
}
@keyframes spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}
</style>
