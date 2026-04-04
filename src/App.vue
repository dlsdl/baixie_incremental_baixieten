<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch } from 'vue'
import pako from 'pako'

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
  bcAuto1Unlocked: boolean; bcAuto2Unlocked: boolean; bcAuto3Unlocked: boolean; bcAuto4Unlocked: boolean
  bcAuto5Unlocked: boolean; bcAuto6Unlocked: boolean; bcAuto7Unlocked: boolean; bcAuto8Unlocked: boolean
  bjAuto1Unlocked: boolean; bjAuto2Unlocked: boolean; bjAuto3Unlocked: boolean; bjAuto4Unlocked: boolean
  bjAuto5Unlocked: boolean; bjAuto6Unlocked: boolean; bjAuto7Unlocked: boolean; bjAuto8Unlocked: boolean
  bqAuto1Unlocked: boolean; bqAuto2Unlocked: boolean; bqAuto3Unlocked: boolean; bqAuto4Unlocked: boolean
  bqAuto5Unlocked: boolean; bqAuto6Unlocked: boolean; bqAuto7Unlocked: boolean; bqAuto8Unlocked: boolean
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
  bcAuto1Unlocked: false, bcAuto2Unlocked: false, bcAuto3Unlocked: false, bcAuto4Unlocked: false,
  bcAuto5Unlocked: false, bcAuto6Unlocked: false, bcAuto7Unlocked: false, bcAuto8Unlocked: false,
  bjAuto1Unlocked: false, bjAuto2Unlocked: false, bjAuto3Unlocked: false, bjAuto4Unlocked: false,
  bjAuto5Unlocked: false, bjAuto6Unlocked: false, bjAuto7Unlocked: false, bjAuto8Unlocked: false,
  bqAuto1Unlocked: false, bqAuto2Unlocked: false, bqAuto3Unlocked: false, bqAuto4Unlocked: false,
  bqAuto5Unlocked: false, bqAuto6Unlocked: false, bqAuto7Unlocked: false, bqAuto8Unlocked: false,
  gameEnded: false
}

const game = ref<GameState>({ ...defaultSave })

const activeTab = ref('baixie')
const showBgImage = ref(true)
const isBcUnlocked = ref(false)
const isBjUnlocked = ref(false)
const isBqUnlocked = ref(false)
const isBwUnlocked = ref(false)

const formatNumber = (num: number): string => {
  if (num === 0) return '0'
  if (num < 1000) return num.toFixed(1)
  const exp = Math.floor(Math.log10(num))
  const mantissa = num / Math.pow(10, exp)
  return `${mantissa.toFixed(3)}e${exp}`
}

// 升级花费乘数常量
const UPGRADE_COST_MULTIPLIER_1_3 = 1.25
const UPGRADE_COST_MULTIPLIER_4 = 10

// 升级效果乘数常量
const UPGRADE_1_OR_2_HUNDREDS_MULTIPLIER = 10
const UPGRADE_1_OR_2_TENS_MULTIPLIER = 2
const UPGRADE_3_HUNDREDS_MULTIPLIER = 3.5
const UPGRADE_3_TENS_MULTIPLIER = 1.5

// 升级花费计算
const getUpgradeCost = (level: number, baseCost: number, upgradeType: number): number => {
  const multiplier = upgradeType === 4 ? UPGRADE_COST_MULTIPLIER_4 : UPGRADE_COST_MULTIPLIER_1_3
  return Math.floor(baseCost * Math.pow(multiplier, level))
}

//升级经验需求计算
const getBlUpgradeCost = (bl: number): number => bl === 0 ? 1 : 40 * bl * Math.pow(1.25, bl)

// 升级效果计算
const getUpgrade1Or2Effect = (level: number): number => {
  let effect = level
  const hundreds = Math.floor(level / 100)
  const tens = Math.floor(level / 10)
  effect *= Math.pow(UPGRADE_1_OR_2_HUNDREDS_MULTIPLIER, hundreds)
  effect *= Math.pow(UPGRADE_1_OR_2_TENS_MULTIPLIER, tens)
  return 1 + effect
}

const getUpgrade3Effect = (level: number): number => {
  let effect = level * 0.1
  const hundreds = Math.floor(level / 100)
  const tens = Math.floor(level / 10)
  effect *= Math.pow(UPGRADE_3_HUNDREDS_MULTIPLIER, hundreds)
  effect *= Math.pow(UPGRADE_3_TENS_MULTIPLIER, tens)
  return 1 + effect
}

const getUpgrade4Effect = (level: number): number => 1 + Math.min(level, 9)

const getBlBonus = (): number => game.value.bl === 0 ? 1 : Math.floor(game.value.bl * Math.pow(1.05, game.value.bl))
const getBfBonus = (): number => game.value.bf === 0 ? 1 : Math.floor(game.value.bf * Math.pow(1.05, game.value.bf))
const getBmBonus = (): number => game.value.bm === 0 ? 1 : Math.floor(game.value.bm * Math.pow(1.05, game.value.bm))
const getBsBonus = (): number => game.value.bs === 0 ? 1 : Math.floor(game.value.bs * Math.pow(1.05, game.value.bs))
const getByResetReward = (): number => game.value.bl < 40 ? 0 : 10 * Math.pow(1.04, game.value.bl - 40) * getUpgrade3Effect(game.value.bxUpgrade3) * getBfBonus()
const getBzResetReward = (): number => game.value.bl < 120 ? 0 : 10 * Math.pow(1.03, game.value.bl - 120) * getUpgrade3Effect(game.value.byUpgrade3) * getBfBonus()
const getBaResetReward = (): number => game.value.bl < 240 ? 0 : 10 * Math.pow(1.02, game.value.bl - 240) * getUpgrade3Effect(game.value.bzUpgrade3) * getBfBonus()
const getBcResetReward = (): number => game.value.bl < 400 ? 1 : 10 * Math.pow(1.01, game.value.bl - 400) * getUpgrade3Effect(game.value.bzUpgrade3) 
//const getBdResetReward = (): number => game.value.bf < 40 ? 0 : 10 * Math.pow(1.04, game.value.bf - 40) * getUpgrade3Effect(game.value.bcUpgrade3)
const getBjResetReward = (): number => game.value.bf < 120 ? 0 : 10 * Math.pow(1.03, game.value.bf - 120) * getUpgrade3Effect(game.value.bnUpgrade3)
const getBqResetReward = (): number => game.value.bf < 240 ? 0 : 10 * Math.pow(1.02, game.value.bf - 240) * getUpgrade3Effect(game.value.boUpgrade3)

const checkUnlocks = () => {
  if (game.value.bl >= 400) isBcUnlocked.value = true
  if (game.value.bf >= 400) isBjUnlocked.value = true
  if (game.value.bm >= 400) isBqUnlocked.value = true
  if (game.value.bs >= 400) isBwUnlocked.value = true
}
const getBwBonus = (): number => game.value.bw + 1

// 拜谢层级点击倍率
const getBxClickMultiplier = (): number => {
  return getUpgrade1Or2Effect(game.value.bxUpgrade1) * getUpgrade1Or2Effect(game.value.byUpgrade1) * getUpgrade1Or2Effect(game.value.bzUpgrade1) * getUpgrade1Or2Effect(game.value.baUpgrade1) * getBlBonus() * getBwBonus() * getBfBonus()
}

const getBeClickMultiplier = (): number => {
  return getUpgrade1Or2Effect(game.value.bxUpgrade2) * getUpgrade1Or2Effect(game.value.byUpgrade2) * getUpgrade1Or2Effect(game.value.bzUpgrade2) * getUpgrade1Or2Effect(game.value.baUpgrade2) * getBwBonus() * getBfBonus()
}

// 拠谦层级点击倍率
const getBcClickMultiplier = (): number => {
  return getUpgrade3Effect(game.value.baUpgrade3) * getUpgrade1Or2Effect(game.value.bcUpgrade1) * getUpgrade1Or2Effect(game.value.bnUpgrade1) * getUpgrade1Or2Effect(game.value.boUpgrade1) * getUpgrade1Or2Effect(game.value.bpUpgrade1) * getBwBonus()
}

const getBdClickMultiplier = (): number => {
  return getUpgrade3Effect(game.value.baUpgrade3) * getUpgrade1Or2Effect(game.value.bcUpgrade2) * getUpgrade1Or2Effect(game.value.bnUpgrade2) * getUpgrade1Or2Effect(game.value.boUpgrade2) * getUpgrade1Or2Effect(game.value.bpUpgrade2) * getBwBonus()
}

const autoUnlocked = (i: number): boolean => {
  const key = `bxAuto${i}Unlocked` as keyof GameState
  return game.value[key] as boolean
}

const clickBx = (clickMultiplier: number = 1) => {
  if (game.value.gameEnded) return
  game.value.bx += 1 * getBxClickMultiplier() * clickMultiplier
  game.value.be += 1 * getBeClickMultiplier() * clickMultiplier
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

const upgradeBc = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = 0
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bcUpgrade1, 30, 1)
      if (game.value.bc >= cost) { game.value.bc -= cost; game.value.bcUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bcUpgrade2, 300, 2)
      if (game.value.bc >= cost) { game.value.bc -= cost; game.value.bcUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bcUpgrade3, 3000, 3)
      if (game.value.bc >= cost) { game.value.bc -= cost; game.value.bcUpgrade3++ }
      break
    case 4:
      if (game.value.bcUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bcUpgrade4, 30000, 4)
        if (game.value.bc >= cost) { game.value.bc -= cost; game.value.bcUpgrade4++ }
      }
      break
  }
}

// const upgradeBj = (upgrade: number) => {
//   if (game.value.gameEnded) return
//   let cost = 0
//   switch (upgrade) {
//     case 1:
//       cost = getUpgradeCost(game.value.bjUpgrade1, 10, 1)
//       if (game.value.bj >= cost) { game.value.bj -= cost; game.value.bjUpgrade1++ }
//       break
//     case 2:
//       cost = getUpgradeCost(game.value.bjUpgrade2, 100, 2)
//       if (game.value.bj >= cost) { game.value.bj -= cost; game.value.bjUpgrade2++ }
//       break
//     case 3:
//       cost = getUpgradeCost(game.value.bjUpgrade3, 1000, 3)
//       if (game.value.bj >= cost) { game.value.bj -= cost; game.value.bjUpgrade3++ }
//       break
//     case 4:
//       if (game.value.bjUpgrade4 < 9) {
//         cost = getUpgradeCost(game.value.bjUpgrade4, 10000, 4)
//         if (game.value.bj >= cost) { game.value.bj -= cost; game.value.bjUpgrade4++ }
//       }
//       break
//   }
// }

// const upgradeBn = (upgrade: number) => {
//   if (game.value.gameEnded) return
//   let cost = 0
//   switch (upgrade) {
//     case 1:
//       cost = getUpgradeCost(game.value.bnUpgrade1, 10, 1)
//       if (game.value.bn >= cost) { game.value.bn -= cost; game.value.bnUpgrade1++ }
//       break
//     case 2:
//       cost = getUpgradeCost(game.value.bnUpgrade2, 100, 2)
//       if (game.value.bn >= cost) { game.value.bn -= cost; game.value.bnUpgrade2++ }
//       break
//     case 3:
//       cost = getUpgradeCost(game.value.bnUpgrade3, 1000, 3)
//       if (game.value.bn >= cost) { game.value.bn -= cost; game.value.bnUpgrade3++ }
//       break
//     case 4:
//       if (game.value.bnUpgrade4 < 9) {
//         cost = getUpgradeCost(game.value.bnUpgrade4, 10000, 4)
//         if (game.value.bn >= cost) { game.value.bn -= cost; game.value.bnUpgrade4++ }
//       }
//       break
//   }
// }

// const upgradeBo = (upgrade: number) => {
//   if (game.value.gameEnded) return
//   let cost = 0
//   switch (upgrade) {
//     case 1:
//       cost = getUpgradeCost(game.value.boUpgrade1, 10, 1)
//       if (game.value.bo >= cost) { game.value.bo -= cost; game.value.boUpgrade1++ }
//       break
//     case 2:
//       cost = getUpgradeCost(game.value.boUpgrade2, 100, 2)
//       if (game.value.bo >= cost) { game.value.bo -= cost; game.value.boUpgrade2++ }
//       break
//     case 3:
//       cost = getUpgradeCost(game.value.boUpgrade3, 1000, 3)
//       if (game.value.bo >= cost) { game.value.bo -= cost; game.value.boUpgrade3++ }
//       break
//     case 4:
//       if (game.value.boUpgrade4 < 9) {
//         cost = getUpgradeCost(game.value.boUpgrade4, 10000, 4)
//         if (game.value.bo >= cost) { game.value.bo -= cost; game.value.boUpgrade4++ }
//       }
//       break
//   }
// }

// const upgradeBq = (upgrade: number) => {
//   if (game.value.gameEnded) return
//   let cost = 0
//   switch (upgrade) {
//     case 1:
//       cost = getUpgradeCost(game.value.bqUpgrade1, 10, 1)
//       if (game.value.bq >= cost) { game.value.bq -= cost; game.value.bqUpgrade1++ }
//       break
//     case 2:
//       cost = getUpgradeCost(game.value.bqUpgrade2, 100, 2)
//       if (game.value.bq >= cost) { game.value.bq -= cost; game.value.bqUpgrade2++ }
//       break
//     case 3:
//       cost = getUpgradeCost(game.value.bqUpgrade3, 1000, 3)
//       if (game.value.bq >= cost) { game.value.bq -= cost; game.value.bqUpgrade3++ }
//       break
//     case 4:
//       if (game.value.bqUpgrade4 < 9) {
//         cost = getUpgradeCost(game.value.bqUpgrade4, 10000, 4)
//         if (game.value.bq >= cost) { game.value.bq -= cost; game.value.bqUpgrade4++ }
//       }
//       break
//   }
// }

// const upgradeBt = (upgrade: number) => {
//   if (game.value.gameEnded) return
//   let cost = 0
//   switch (upgrade) {
//     case 1:
//       cost = getUpgradeCost(game.value.btUpgrade1, 10, 1)
//       if (game.value.bt >= cost) { game.value.bt -= cost; game.value.btUpgrade1++ }
//       break
//     case 2:
//       cost = getUpgradeCost(game.value.btUpgrade2, 100, 2)
//       if (game.value.bt >= cost) { game.value.bt -= cost; game.value.btUpgrade2++ }
//       break
//     case 3:
//       cost = getUpgradeCost(game.value.btUpgrade3, 1000, 3)
//       if (game.value.bt >= cost) { game.value.bt -= cost; game.value.btUpgrade3++ }
//       break
//     case 4:
//       if (game.value.btUpgrade4 < 9) {
//         cost = getUpgradeCost(game.value.btUpgrade4, 10000, 4)
//         if (game.value.bt >= cost) { game.value.bt -= cost; game.value.btUpgrade4++ }
//       }
//       break
//   }
// }

// const upgradeBu = (upgrade: number) => {
//   if (game.value.gameEnded) return
//   let cost = 0
//   switch (upgrade) {
//     case 1:
//       cost = getUpgradeCost(game.value.buUpgrade1, 10, 1)
//       if (game.value.bu >= cost) { game.value.bu -= cost; game.value.buUpgrade1++ }
//       break
//     case 2:
//       cost = getUpgradeCost(game.value.buUpgrade2, 100, 2)
//       if (game.value.bu >= cost) { game.value.bu -= cost; game.value.buUpgrade2++ }
//       break
//     case 3:
//       cost = getUpgradeCost(game.value.buUpgrade3, 1000, 3)
//       if (game.value.bu >= cost) { game.value.bu -= cost; game.value.buUpgrade3++ }
//       break
//     case 4:
//       if (game.value.buUpgrade4 < 9) {
//         cost = getUpgradeCost(game.value.buUpgrade4, 10000, 4)
//         if (game.value.bu >= cost) { game.value.bu -= cost; game.value.buUpgrade4++ }
//       }
//       break
//   }
// }

// const upgradeBv = (upgrade: number) => {
//   if (game.value.gameEnded) return
//   let cost = 0
//   switch (upgrade) {
//     case 1:
//       cost = getUpgradeCost(game.value.bvUpgrade1, 10, 1)
//       if (game.value.bv >= cost) { game.value.bv -= cost; game.value.bvUpgrade1++ }
//       break
//     case 2:
//       cost = getUpgradeCost(game.value.bvUpgrade2, 100, 2)
//       if (game.value.bv >= cost) { game.value.bv -= cost; game.value.bvUpgrade2++ }
//       break
//     case 3:
//       cost = getUpgradeCost(game.value.bvUpgrade3, 1000, 3)
//       if (game.value.bv >= cost) { game.value.bv -= cost; game.value.bvUpgrade3++ }
//       break
//     case 4:
//       if (game.value.bvUpgrade4 < 9) {
//         cost = getUpgradeCost(game.value.bvUpgrade4, 10000, 4)
//         if (game.value.bv >= cost) { game.value.bv -= cost; game.value.bvUpgrade4++ }
//       }
//       break
//   }
// }

const upgradeBp = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = 0
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bpUpgrade1, 10, 1)
      if (game.value.bp >= cost) { game.value.bp -= cost; game.value.bpUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bpUpgrade2, 100, 2)
      if (game.value.bp >= cost) { game.value.bp -= cost; game.value.bpUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bpUpgrade3, 1000, 3)
      if (game.value.bp >= cost) { game.value.bp -= cost; game.value.bpUpgrade3++ }
      break
    case 4:
      if (game.value.bpUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bpUpgrade4, 10000, 4)
        if (game.value.bp >= cost) { game.value.bp -= cost; game.value.bpUpgrade4++ }
      }
      break
  }
}

// 拤谪层级升级函数
// const upgradeBj = (upgrade: number) => {
//   if (game.value.gameEnded) return
//   let cost = 0
//   switch (upgrade) {
//     case 1:
//       cost = getUpgradeCost(game.value.bjUpgrade1, 10, 1)
//       if (game.value.bj >= cost) { game.value.bj -= cost; game.value.bjUpgrade1++ }
//       break
//     case 2:
//       cost = getUpgradeCost(game.value.bjUpgrade2, 100, 2)
//       if (game.value.bj >= cost) { game.value.bj -= cost; game.value.bjUpgrade2++ }
//       break
//     case 3:
//       cost = getUpgradeCost(game.value.bjUpgrade3, 1000, 3)
//       if (game.value.bj >= cost) { game.value.bj -= cost; game.value.bjUpgrade3++ }
//       break
//     case 4:
//       if (game.value.bjUpgrade4 < 9) {
//         cost = getUpgradeCost(game.value.bjUpgrade4, 10000, 4)
//         if (game.value.bj >= cost) { game.value.bj -= cost; game.value.bjUpgrade4++ }
//       }
//       break
//   }
// }

const upgradeBn = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = 0
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bnUpgrade1, 10, 1)
      if (game.value.bn >= cost) { game.value.bn -= cost; game.value.bnUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bnUpgrade2, 100, 2)
      if (game.value.bn >= cost) { game.value.bn -= cost; game.value.bnUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bnUpgrade3, 1000, 3)
      if (game.value.bn >= cost) { game.value.bn -= cost; game.value.bnUpgrade3++ }
      break
    case 4:
      if (game.value.bnUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bnUpgrade4, 10000, 4)
        if (game.value.bn >= cost) { game.value.bn -= cost; game.value.bnUpgrade4++ }
      }
      break
  }
}

const upgradeBo = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = 0
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.boUpgrade1, 10, 1)
      if (game.value.bo >= cost) { game.value.bo -= cost; game.value.boUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.boUpgrade2, 100, 2)
      if (game.value.bo >= cost) { game.value.bo -= cost; game.value.boUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.boUpgrade3, 1000, 3)
      if (game.value.bo >= cost) { game.value.bo -= cost; game.value.boUpgrade3++ }
      break
    case 4:
      if (game.value.boUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.boUpgrade4, 10000, 4)
        if (game.value.bo >= cost) { game.value.bo -= cost; game.value.boUpgrade4++ }
      }
      break
  }
}

// 拨谮层级升级函数
// const upgradeBq = (upgrade: number) => {
//   if (game.value.gameEnded) return
//   let cost = 0
//   switch (upgrade) {
//     case 1:
//       cost = getUpgradeCost(game.value.bqUpgrade1, 10, 1)
//       if (game.value.bq >= cost) { game.value.bq -= cost; game.value.bqUpgrade1++ }
//       break
//     case 2:
//       cost = getUpgradeCost(game.value.bqUpgrade2, 100, 2)
//       if (game.value.bq >= cost) { game.value.bq -= cost; game.value.bqUpgrade2++ }
//       break
//     case 3:
//       cost = getUpgradeCost(game.value.bqUpgrade3, 1000, 3)
//       if (game.value.bq >= cost) { game.value.bq -= cost; game.value.bqUpgrade3++ }
//       break
//     case 4:
//       if (game.value.bqUpgrade4 < 9) {
//         cost = getUpgradeCost(game.value.bqUpgrade4, 10000, 4)
//         if (game.value.bq >= cost) { game.value.bq -= cost; game.value.bqUpgrade4++ }
//       }
//       break
//   }
// }

// const upgradeBt = (upgrade: number) => {
//   if (game.value.gameEnded) return
//   let cost = 0
//   switch (upgrade) {
//     case 1:
//       cost = getUpgradeCost(game.value.btUpgrade1, 10, 1)
//       if (game.value.bt >= cost) { game.value.bt -= cost; game.value.btUpgrade1++ }
//       break
//     case 2:
//       cost = getUpgradeCost(game.value.btUpgrade2, 100, 2)
//       if (game.value.bt >= cost) { game.value.bt -= cost; game.value.btUpgrade2++ }
//       break
//     case 3:
//       cost = getUpgradeCost(game.value.btUpgrade3, 1000, 3)
//       if (game.value.bt >= cost) { game.value.bt -= cost; game.value.btUpgrade3++ }
//       break
//     case 4:
//       if (game.value.btUpgrade4 < 9) {
//         cost = getUpgradeCost(game.value.btUpgrade4, 10000, 4)
//         if (game.value.bt >= cost) { game.value.bt -= cost; game.value.btUpgrade4++ }
//       }
//       break
//   }
// }

// const upgradeBu = (upgrade: number) => {
//   if (game.value.gameEnded) return
//   let cost = 0
//   switch (upgrade) {
//     case 1:
//       cost = getUpgradeCost(game.value.buUpgrade1, 10, 1)
//       if (game.value.bu >= cost) { game.value.bu -= cost; game.value.buUpgrade1++ }
//       break
//     case 2:
//       cost = getUpgradeCost(game.value.buUpgrade2, 100, 2)
//       if (game.value.bu >= cost) { game.value.bu -= cost; game.value.buUpgrade2++ }
//       break
//     case 3:
//       cost = getUpgradeCost(game.value.buUpgrade3, 1000, 3)
//       if (game.value.bu >= cost) { game.value.bu -= cost; game.value.buUpgrade3++ }
//       break
//     case 4:
//       if (game.value.buUpgrade4 < 9) {
//         cost = getUpgradeCost(game.value.buUpgrade4, 10000, 4)
//         if (game.value.bu >= cost) { game.value.bu -= cost; game.value.buUpgrade4++ }
//       }
//       break
//   }
// }

// const upgradeBv = (upgrade: number) => {
//   if (game.value.gameEnded) return
//   let cost = 0
//   switch (upgrade) {
//     case 1:
//       cost = getUpgradeCost(game.value.bvUpgrade1, 10, 1)
//       if (game.value.bv >= cost) { game.value.bv -= cost; game.value.bvUpgrade1++ }
//       break
//     case 2:
//       cost = getUpgradeCost(game.value.bvUpgrade2, 100, 2)
//       if (game.value.bv >= cost) { game.value.bv -= cost; game.value.bvUpgrade2++ }
//       break
//     case 3:
//       cost = getUpgradeCost(game.value.bvUpgrade3, 1000, 3)
//       if (game.value.bv >= cost) { game.value.bv -= cost; game.value.bvUpgrade3++ }
//       break
//     case 4:
//       if (game.value.bvUpgrade4 < 9) {
//         cost = getUpgradeCost(game.value.bvUpgrade4, 10000, 4)
//         if (game.value.bv >= cost) { game.value.bv -= cost; game.value.bvUpgrade4++ }
//       }
//       break
//   }
// }

const autoUpgradeBl = () => {
  if (game.value.gameEnded) return
  let cost = getBlUpgradeCost(game.value.bl)
  while (game.value.be >= cost && cost > 0) {
    game.value.be -= cost
    game.value.bl++
    cost = getBlUpgradeCost(game.value.bl)
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

const resetBc = () => {
  if (game.value.bf < 40) return
  game.value.bc += getBcResetReward()
  game.value.bx = 0; game.value.be = 0; game.value.bl = 1; game.value.by = 0; game.value.bz = 0; game.value.ba = 0
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
  game.value.baUpgrade1 = 0; game.value.baUpgrade2 = 0; game.value.baUpgrade3 = 0; game.value.baUpgrade4 = 0
  game.value.bxAuto1Unlocked = false; game.value.bxAuto2Unlocked = false; game.value.bxAuto3Unlocked = false; game.value.bxAuto4Unlocked = false
  game.value.bxAuto5Unlocked = false; game.value.bxAuto6Unlocked = false; game.value.bxAuto7Unlocked = false; game.value.bxAuto8Unlocked = false
}

const getBfUpgradeCost = (bf: number): number => bf === 0 ? 3 : 120 * bf * Math.pow(1.25, bf)

const autoUpgradeBf = () => {
  if (game.value.gameEnded) return
  let cost = getBfUpgradeCost(game.value.bf)
  while (game.value.bd >= cost && cost > 0) {
    game.value.bd -= cost
    game.value.bf++
    cost = getBfUpgradeCost(game.value.bf)
  }
}

// 拠谦层级自动化
const upgradeBcAuto = (autoIndex: number) => {
  if (game.value.gameEnded) return
  const autoKey = `bcAuto${autoIndex}Unlocked` as keyof GameState
  const isUnlocked = game.value[autoKey] as unknown as boolean
  const unlockCosts = [1e4, 1e16, 1e16, 1e16, 1e10, 1e10, 1e10, 1e10]
  const unlockResources = [game.value.bc, game.value.bg, game.value.bh, game.value.bi, game.value.bc, game.value.bg, game.value.bh, game.value.bi]
  if (!isUnlocked) {
    if (unlockResources[autoIndex - 1] >= unlockCosts[autoIndex - 1]) {
      ;(game.value[autoKey] as unknown as boolean) = true
    }
  }
}

const autoClickBc = () => {
  if (!game.value.bcAuto1Unlocked) return
  clickBc()
}

const autoGetBcResetResources = () => {
  if (game.value.bcAuto2Unlocked && game.value.bf >= 40) {
    game.value.bc += getBcResetReward() / 10
  }
  if (game.value.bcAuto3Unlocked && game.value.bf >= 120) {
    game.value.bg += getBjResetReward() / 10
  }
  if (game.value.bcAuto4Unlocked && game.value.bf >= 240) {
    game.value.bh += getBqResetReward() / 10
  }
}

const autoBuyBcUpgrades = () => {
  if (game.value.bcAuto5Unlocked) {
    const upgrades = [
      { level: game.value.bcUpgrade1, base: 30 },
      { level: game.value.bcUpgrade2, base: 300 },
      { level: game.value.bcUpgrade3, base: 3000 },
      { level: game.value.bcUpgrade4, base: 30000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bc, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.bcUpgrade1 += maxBuy; break
          case 1: game.value.bcUpgrade2 += maxBuy; break
          case 2: game.value.bcUpgrade3 += maxBuy; break
          case 3: game.value.bcUpgrade4 = Math.min(game.value.bcUpgrade4 + maxBuy, 9); break
        }
      }
    })
  }
}

const resetBj = () => {
  if (game.value.bf < 120) return
  game.value.bj += getBjResetReward()
  game.value.bc = 0; game.value.bd = 0; game.value.bf = 1; game.value.bg = 0; game.value.bh = 0; game.value.bi = 0
  game.value.bcUpgrade1 = 0; game.value.bcUpgrade2 = 0; game.value.bcUpgrade3 = 0; game.value.bcUpgrade4 = 0
  game.value.bgUpgrade1 = 0; game.value.bgUpgrade2 = 0; game.value.bgUpgrade3 = 0; game.value.bgUpgrade4 = 0
  game.value.bhUpgrade1 = 0; game.value.bhUpgrade2 = 0; game.value.bhUpgrade3 = 0; game.value.bhUpgrade4 = 0
  game.value.biUpgrade1 = 0; game.value.biUpgrade2 = 0; game.value.biUpgrade3 = 0; game.value.biUpgrade4 = 0
}

const resetBq = () => {
  if (game.value.bf < 240) return
  game.value.bq += getBqResetReward()
  game.value.bc = 0; game.value.bd = 0; game.value.bf = 1; game.value.bg = 0; game.value.bh = 0; game.value.bi = 0; game.value.bj = 0; game.value.bk = 0; game.value.bm = 1; game.value.bn = 0; game.value.bo = 0; game.value.bp = 0
  game.value.bcUpgrade1 = 0; game.value.bcUpgrade2 = 0; game.value.bcUpgrade3 = 0; game.value.bcUpgrade4 = 0
  game.value.bgUpgrade1 = 0; game.value.bgUpgrade2 = 0; game.value.bgUpgrade3 = 0; game.value.bgUpgrade4 = 0
  game.value.bhUpgrade1 = 0; game.value.bhUpgrade2 = 0; game.value.bhUpgrade3 = 0; game.value.bhUpgrade4 = 0
  game.value.biUpgrade1 = 0; game.value.biUpgrade2 = 0; game.value.biUpgrade3 = 0; game.value.biUpgrade4 = 0
  game.value.bjUpgrade1 = 0; game.value.bjUpgrade2 = 0; game.value.bjUpgrade3 = 0; game.value.bjUpgrade4 = 0
  game.value.bnUpgrade1 = 0; game.value.bnUpgrade2 = 0; game.value.bnUpgrade3 = 0; game.value.bnUpgrade4 = 0
  game.value.boUpgrade1 = 0; game.value.boUpgrade2 = 0; game.value.boUpgrade3 = 0; game.value.boUpgrade4 = 0
  game.value.bpUpgrade1 = 0; game.value.bpUpgrade2 = 0; game.value.bpUpgrade3 = 0; game.value.bpUpgrade4 = 0
}

const clickBc = (clickMultiplier: number = 1) => {
  if (game.value.bl < 400) return
  game.value.bc += 1 * getBcClickMultiplier() * clickMultiplier
  game.value.bd += 1 * getBdClickMultiplier() * clickMultiplier
  game.value.bx = 0; game.value.be = 0; game.value.bl = 1; game.value.by = 0; game.value.bz = 0; game.value.ba = 0
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
  game.value.baUpgrade1 = 0; game.value.baUpgrade2 = 0; game.value.baUpgrade3 = 0; game.value.baUpgrade4 = 0
}

const clickBj = (clickMultiplier: number = 1) => {
  if (game.value.gameEnded) return
  const bjMultiplier = getUpgrade1Or2Effect(game.value.bjUpgrade1) * getUpgrade1Or2Effect(game.value.btUpgrade1) * getUpgrade1Or2Effect(game.value.buUpgrade1) * getUpgrade1Or2Effect(game.value.bvUpgrade1) * getBmBonus() * getBwBonus()
  const bkMultiplier = getUpgrade1Or2Effect(game.value.bjUpgrade2) * getUpgrade1Or2Effect(game.value.btUpgrade2) * getUpgrade1Or2Effect(game.value.buUpgrade2) * getUpgrade1Or2Effect(game.value.bvUpgrade2) * getBwBonus()
  game.value.bj += 1 * bjMultiplier * clickMultiplier
  game.value.bk += 1 * bkMultiplier * clickMultiplier
}

const clickBq = (clickMultiplier: number = 1) => {
  if (game.value.gameEnded) return
  const bqMultiplier = getUpgrade1Or2Effect(game.value.bqUpgrade1) * getUpgrade1Or2Effect(game.value.btUpgrade1) * getUpgrade1Or2Effect(game.value.buUpgrade1) * getUpgrade1Or2Effect(game.value.bvUpgrade1) * getBsBonus() * getBwBonus()
  const brMultiplier = getUpgrade1Or2Effect(game.value.bqUpgrade2) * getUpgrade1Or2Effect(game.value.btUpgrade2) * getUpgrade1Or2Effect(game.value.buUpgrade2) * getUpgrade1Or2Effect(game.value.bvUpgrade2) * getBwBonus()
  game.value.bq += 1 * bqMultiplier * clickMultiplier
  game.value.br += 1 * brMultiplier * clickMultiplier
}

// 拜谢层级自动化
const upgradeBxAuto = (autoIndex: number) => {
  if (game.value.gameEnded) return
  const autoKey = `bxAuto${autoIndex}Unlocked` as keyof GameState
  const isUnlocked = game.value[autoKey] as unknown as boolean
  const unlockCosts = [1e3, 1e15, 1e15, 1e15, 1e9, 1e9, 1e9, 1e9]
  const unlockResources = [game.value.bx, game.value.by, game.value.bz, game.value.ba, game.value.bx, game.value.by, game.value.bz, game.value.ba]
  if (!isUnlocked) {
    if (unlockResources[autoIndex - 1] >= unlockCosts[autoIndex - 1]) {
      ;(game.value[autoKey] as unknown as boolean) = true
    }
  }
}

const autoClickBx = () => {
  if (!game.value.bxAuto1Unlocked) return
  const mult = getUpgrade4Effect(game.value.bxUpgrade4) * getUpgrade4Effect(game.value.byUpgrade4) * getUpgrade4Effect(game.value.bzUpgrade4) * getUpgrade4Effect(game.value.baUpgrade4)
  clickBx(mult/10)
}

const autoGetResetResources = () => {
  if (game.value.bxAuto2Unlocked) {
    game.value.by += getByResetReward() * getBwBonus() / 100
  }
  if (game.value.bxAuto3Unlocked) {
    game.value.bz += getBzResetReward() * getBwBonus() / 100
  }
  if (game.value.bxAuto4Unlocked) {
    game.value.ba += getBaResetReward() * getBwBonus() / 100
  }
}

const calculateMaxPurchases = (resource: number, baseCost: number, multiplier: number, currentLevel: number): number => {
  if (resource < baseCost * Math.pow(multiplier, currentLevel)) return 0
  const r = multiplier
  // 使用等比数列求和公式反解最大购买数量
  // totalCost = baseCost * r^currentLevel * (r^n - 1) / (r - 1) <= resource
  // 解得: n <= log_r(1 + resource * (r - 1) / (baseCost * r^currentLevel))
  const firstTermCost = baseCost * Math.pow(r, currentLevel)
  const maxN = Math.log(1 + resource * (r - 1) / firstTermCost) / Math.log(r)
  return Math.min(Math.floor(maxN), 1000)
}

const autoBuyUpgrades = () => {
  // 自动化 5：自动购买 拜谢 升级
  if (game.value.bxAuto5Unlocked) {
    const upgrades = [
      { level: game.value.bxUpgrade1, base: 10 },
      { level: game.value.bxUpgrade2, base: 100 },
      { level: game.value.bxUpgrade3, base: 1000 },
      { level: game.value.bxUpgrade4, base: 10000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bx, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.bxUpgrade1 += maxBuy; break
          case 1: game.value.bxUpgrade2 += maxBuy; break
          case 2: game.value.bxUpgrade3 += maxBuy; break
          case 3: game.value.bxUpgrade4 = Math.min(Math.floor(Math.log(game.value.bx/1000 + 1)) , 9); break
        }
      }
    })
  }
  // 自动化 6：自动购买 拝谣 升级
  if (game.value.bxAuto6Unlocked) {
    const upgrades = [
      { level: game.value.byUpgrade1, base: 10 },
      { level: game.value.byUpgrade2, base: 100 },
      { level: game.value.byUpgrade3, base: 1000 },
      { level: game.value.byUpgrade4, base: 10000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.by, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.byUpgrade1 += maxBuy; break
          case 1: game.value.byUpgrade2 += maxBuy; break
          case 2: game.value.byUpgrade3 += maxBuy; break
          case 3: game.value.byUpgrade4 = Math.min(Math.floor(Math.log(game.value.by/1000 + 1)) , 9); break
        }
      }
    })
  }
  // 自动化 7：自动购买 拞谤 升级
  if (game.value.bxAuto7Unlocked) {
    const upgrades = [
      { level: game.value.bzUpgrade1, base: 10 },
      { level: game.value.bzUpgrade2, base: 100 },
      { level: game.value.bzUpgrade3, base: 1000 },
      { level: game.value.bzUpgrade4, base: 10000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bz, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.bzUpgrade1 += maxBuy; break
          case 1: game.value.bzUpgrade2 += maxBuy; break
          case 2: game.value.bzUpgrade3 += maxBuy; break
          case 3: game.value.bzUpgrade4 = Math.min(Math.floor(Math.log(game.value.bz/1000 + 1)) , 9); break
        }
      }
    })
  }
  // 自动化 8：自动购买 拟谥 升级
  if (game.value.bxAuto8Unlocked) {
    const upgrades = [
      { level: game.value.baUpgrade1, base: 10 },
      { level: game.value.baUpgrade2, base: 100 },
      { level: game.value.baUpgrade3, base: 1000 },
      { level: game.value.baUpgrade4, base: 10000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.ba, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.baUpgrade1 += maxBuy; break
          case 1: game.value.baUpgrade2 += maxBuy; break
          case 2: game.value.baUpgrade3 += maxBuy; break
          case 3: game.value.baUpgrade4 = Math.min(Math.floor(Math.log(game.value.ba/1000 + 1)) , 9); break
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
    checkUnlocks()
    if (game.value.bxAuto1Unlocked) autoClickBx()
    if (game.value.bcAuto1Unlocked) autoClickBc()
    autoGetResetResources()
    autoGetBcResetResources()
    autoBuyUpgrades()
    autoBuyBcUpgrades()
    autoUpgradeBl()
    autoUpgradeBf()
    if (game.value.bw >= 114514) { game.value.bw = 114514; game.value.gameEnded = true }
  }, 100)
  saveInterval = window.setInterval(() => saveGame(), 5000)
  const animate = () => {
    bgDecorations.value.forEach(deco => {
      deco.rotation += deco.speed
      deco.x += Math.sin(deco.rotation * 0.1) * 0.1
      deco.y += Math.cos(deco.rotation * 0.1) * 0.1
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
  const compressed = pako.deflate(data)
  let binary = ''
  compressed.forEach((byte: number) => { binary += String.fromCharCode(byte) })
  const encoded = btoa(binary)
  const blob = new Blob([encoded], { type: 'text/plain' })
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a')
  a.href = url
  a.download = `baixie_save_${new Date().toISOString()}.txt`
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
      const decoded = atob(e.target?.result as string)
      const binary = new Uint8Array(decoded.length)
      for (let i = 0; i < decoded.length; i++) binary[i] = decoded.charCodeAt(i)
      const decompressed = pako.inflate(binary, { to: 'string' })
      game.value = { ...defaultSave, ...JSON.parse(decompressed) }
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
  game.value.bw += 3 * Math.pow(1.01, game.value.bs - 400) / 10
  game.value.bx = 0; game.value.be = 0; game.value.bl = 1; game.value.by = 0; game.value.bz = 0; game.value.ba = 0
  game.value.bc = 0; game.value.bd = 0; game.value.bf = 1; game.value.bg = 0; game.value.bh = 0; game.value.bi = 0
  game.value.bj = 0; game.value.bk = 0; game.value.bm = 1; game.value.bn = 0; game.value.bo = 0; game.value.bp = 0
  game.value.bq = 0; game.value.br = 0; game.value.bs = 1; game.value.bt = 0; game.value.bu = 0; game.value.bv = 0
  Object.keys(game.value).forEach(key => { if (key.includes('Upgrade')) (game.value[key as keyof GameState] as unknown as number) = 0 })
  game.value.bxAuto1Unlocked = false; game.value.bxAuto2Unlocked = false; game.value.bxAuto3Unlocked = false; game.value.bxAuto4Unlocked = false
  game.value.bxAuto5Unlocked = false; game.value.bxAuto6Unlocked = false; game.value.bxAuto7Unlocked = false; game.value.bxAuto8Unlocked = false
}

onMounted(() => {
  loadGame()
  startGameLoop()
  for (let i = 0; i < 50; i++) {
    bgDecorations.value.push({
      id: i, x: Math.random() * 100, y: Math.random() * 100, size: 50 + Math.random() * 150,
      rotation: Math.random() * 360, speed: (Math.random() - 0.5) * 0.2, opacity: 0.3 + Math.random() * 0.4
    })
  }
})
onUnmounted(() => { clearInterval(gameLoop); clearInterval(saveInterval); cancelAnimationFrame(animationFrame) })
watch(game, () => saveGame(), { deep: true })
</script>

<template>
  <div class="game-container">
        <button 
          @click="showBgImage = !showBgImage" 
          class="tab-btn">
          {{ showBgImage ? '关闭背景' : '开启背景' }}
        </button>
    <div class="bg-decorations" v-if="showBgImage">
      <div v-for="deco in bgDecorations" :key="deco.id" class="bg-decoration"
        :style="{ left: deco.x + '%', top: deco.y + '%', width: deco.size + 'px', height: deco.size + 'px',
          transform: `rotate(${deco.rotation}deg)`, opacity: deco.opacity }">
        <img src="/baixie.png" alt="" />
      </div>
    </div>
    <div class="game-content">
      <h1>拜谢增量拜谢版</h1>
      <br />
      <div v-if="game.gameEnded" class="ending-message">
        <h2>🎉 恭喜通关！你已到达游戏结局 🎉</h2>
        <div class="ending-bg">
          <img src="/baixie.png" alt="" class="ending-img" v-for="i in 5" :key="i" />
        </div>
      </div>
      
      <!-- 标签页导航 -->
      <div class="tab-navigation">
        <button 
          :class="{ active: activeTab === 'baixie' }" 
          @click="activeTab = 'baixie'" 
          class="tab-btn">
          拜谢层级
        </button>
        <button 
          v-if="isBcUnlocked"
          :class="{ active: activeTab === 'juqian' }" 
          @click="activeTab = 'juqian'" 
          class="tab-btn">
          拠谦层级
        </button>
        <button 
          v-if="isBjUnlocked"
          :class="{ active: activeTab === 'jiadi' }" 
          @click="activeTab = 'jiadi'" 
          class="tab-btn">
          拤谪层级
        </button>
        <button 
          v-if="isBqUnlocked"
          :class="{ active: activeTab === 'boxie' }" 
          @click="activeTab = 'boxie'" 
          class="tab-btn">
          拨谮层级
        </button>
        <button 
          v-if="isBwUnlocked"
          :class="{ active: activeTab === 'god' }" 
          @click="activeTab = 'god'" 
          class="tab-btn">
          拜谢之神
        </button>
        <button 
          :class="{ active: activeTab === 'save' }" 
          @click="activeTab = 'save'" 
          class="tab-btn">
          存档系统
        </button>
      </div>
      
      <!-- 拜谢层级标签页 -->
      <div v-show="activeTab === 'baixie'" class="tab-content">
        <h2>拜谢层级</h2>
        <div class="resources">
          <div class="resource">拜谢：<span class="counter">{{ formatNumber(game.bx) }}</span></div>
          <div class="resource">经验：<span class="counter">{{ formatNumber(game.be) }}</span></div>
          <div class="resource">等级：<span class="counter">{{ game.bl }}</span></div>
          <div class="resource">拝谣：<span class="counter">{{ formatNumber(game.by) }}</span></div>
          <div class="resource">拞谤：<span class="counter">{{ formatNumber(game.bz) }}</span></div>
          <div class="resource">拟谥：<span class="counter">{{ formatNumber(game.ba) }}</span></div>
        </div>
        <div class="actions">
          </div>
        <div class="progress-bar">
          <div class="progress-fill" :style="{ width: Math.min((game.be / getBlUpgradeCost(game.bl)) * 100, 100) + '%' }">
            <span class="progress-text" style="position: absolute; left: 50%; transform: translateX(-50%);">{{ formatNumber(game.be) }} / {{ formatNumber(getBlUpgradeCost(game.bl)) }}</span>
          </div>
        </div>
        <span class="bonus">拜谢加成：×{{ formatNumber(getBlBonus()) }}</span>
          <button @click="clickBx(1)" class="reset-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />拜谢一次：获得 {{ formatNumber(getBxClickMultiplier()) }} 拜谢和 {{ formatNumber(getBeClickMultiplier()) }} 经验
          </button>
          <button @click="resetBy" class="reset-btn" :disabled="game.bl < 40">
            <img src="/baixie.png" alt="" class="btn-icon" />拝谣重置(需要 40 拜谢等级)：重置拜谢和拜谢升级，获得 {{ formatNumber(getByResetReward()) }} 拝谣
          </button>
          <button @click="resetBz" class="reset-btn" :disabled="game.bl < 120">
            <img src="/baixie.png" alt="" class="btn-icon" />拞谤重置(需要 120 拜谢等级)：重置拝谣重置的东西、拝谣和拝谣升级，获得 {{ formatNumber(getBzResetReward()) }} 拞谤
          </button>
          <button @click="resetBa" class="reset-btn" :disabled="game.bl < 240">
            <img src="/baixie.png" alt="" class="btn-icon" />拟谥重置(需要 240 拜谢等级)：重置拞谤重置的东西、拞谤和拞谤升级，获得 {{ formatNumber(getBaResetReward()) }} 拟谥
          </button>
        <div class="upgrades">
          <h3>拜谢升级</h3>
          <button @click="upgradeBx(1)" :disabled="game.bx < getUpgradeCost(game.bxUpgrade1, 10, 1)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拜谢获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bxUpgrade1)) }} (Lv.{{ game.bxUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bxUpgrade1, 10, 1)) }} 拜谢
          </button>
          <button @click="upgradeBx(2)" :disabled="game.bx < getUpgradeCost(game.bxUpgrade2, 100, 2)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拜谢经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bxUpgrade2)) }} (Lv.{{ game.bxUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bxUpgrade2, 100, 2)) }} 拜谢
          </button>
          <button @click="upgradeBx(3)" :disabled="game.bx < getUpgradeCost(game.bxUpgrade3, 1000, 3)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拝谣获取 ×{{ formatNumber(getUpgrade3Effect(game.bxUpgrade3)) }} (Lv.{{ game.bxUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bxUpgrade3, 1000, 3)) }} 拜谢
          </button>
          <button @click="upgradeBx(4)" :disabled="game.bx < getUpgradeCost(game.bxUpgrade4, 10000, 4) || game.bxUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bxUpgrade4)) }} (Lv.{{ game.bxUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bxUpgrade4, 10000, 4)) }} 拜谢
          </button>
        </div>
        <div class="upgrades" v-if="game.bl >= 40 || game.by > 0">
          <h3>拝谣升级</h3>
          <button @click="upgradeBy(1)" :disabled="game.by < getUpgradeCost(game.byUpgrade1, 10, 1)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拜谢获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.byUpgrade1)) }} (Lv.{{ game.byUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.byUpgrade1, 10, 1)) }} 拝谣
          </button>
          <button @click="upgradeBy(2)" :disabled="game.by < getUpgradeCost(game.byUpgrade2, 100, 2)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拜谢经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.byUpgrade2)) }} (Lv.{{ game.byUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.byUpgrade2, 100, 2)) }} 拝谣
          </button>
          <button @click="upgradeBy(3)" :disabled="game.by < getUpgradeCost(game.byUpgrade3, 1000, 3)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拞谤获取 ×{{ formatNumber(getUpgrade3Effect(game.byUpgrade3)) }} (Lv.{{ game.byUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.byUpgrade3, 1000, 3)) }} 拝谣
          </button>
          <button @click="upgradeBy(4)" :disabled="game.by < getUpgradeCost(game.byUpgrade4, 10000, 4) || game.byUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.byUpgrade4)) }} (Lv.{{ game.byUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.byUpgrade4, 10000, 4)) }} 拝谣
          </button>
        </div>
        <div class="upgrades" v-if="game.bl >= 120 || game.bz > 0">
          <h3>拞谤升级</h3>
          <button @click="upgradeBz(1)" :disabled="game.bz < getUpgradeCost(game.bzUpgrade1, 10, 1)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拜谢获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bzUpgrade1)) }} (Lv.{{ game.bzUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bzUpgrade1, 10, 1)) }} 拞谤
          </button>
          <button @click="upgradeBz(2)" :disabled="game.bz < getUpgradeCost(game.bzUpgrade2, 100, 2)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拜谢经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bzUpgrade2)) }} (Lv.{{ game.bzUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bzUpgrade2, 100, 2)) }} 拞谤
          </button>
          <button @click="upgradeBz(3)" :disabled="game.bz < getUpgradeCost(game.bzUpgrade3, 1000, 3)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拟谥获取 ×{{ formatNumber(getUpgrade3Effect(game.bzUpgrade3)) }} (Lv.{{ game.bzUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bzUpgrade3, 1000, 3)) }} 拞谤
          </button>
          <button @click="upgradeBz(4)" :disabled="game.bz < getUpgradeCost(game.bzUpgrade4, 10000, 4) || game.bzUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bzUpgrade4)) }} (Lv.{{ game.bzUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bzUpgrade4, 10000, 4)) }} 拞谤
          </button>
        </div>
        <div class="upgrades" v-if="game.bl >= 240 || game.ba > 0">
          <h3>拟谥升级</h3>
          <button @click="upgradeBa(1)" :disabled="game.ba < getUpgradeCost(game.baUpgrade1, 10, 1)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拜谢获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.baUpgrade1)) }} (Lv.{{ game.baUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.baUpgrade1, 10, 1)) }} 拟谥
          </button>
          <button @click="upgradeBa(2)" :disabled="game.ba < getUpgradeCost(game.baUpgrade2, 100, 2)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拜谢经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.baUpgrade2)) }} (Lv.{{ game.baUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.baUpgrade2, 100, 2)) }} 拟谥
          </button>
          <button @click="upgradeBa(3)" :disabled="game.ba < getUpgradeCost(game.baUpgrade3, 1000, 3)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拠谦获取 ×{{ formatNumber(getUpgrade3Effect(game.baUpgrade3)) }} (Lv.{{ game.baUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.baUpgrade3, 1000, 3)) }} 拟谥
          </button>
          <button @click="upgradeBa(4)" :disabled="game.ba < getUpgradeCost(game.baUpgrade4, 10000, 4) || game.baUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.baUpgrade4)) }} (Lv.{{ game.baUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.baUpgrade4, 10000, 4)) }} 拟谥
          </button>
        </div>
        <div class="section">
        <h2>拜谢自动化</h2>
        <div class="auto-upgrades">
          <div v-for="i in 8" :key="i" class="auto-upgrade">
            <button @click="upgradeBxAuto(i)" :class="{ locked: !autoUnlocked(i) }" class="auto-btn">
              <img src="/baixie.png" alt="" class="btn-icon" /><div class="auto-title">{{ autoUnlocked(i) ? `自动化升级${i} 已解锁` : `自动化升级${i}` }}</div>
              <div class="auto-desc">
                <span v-if="i===1">每秒自动点击拜谢一次按钮</span>
                <span v-else-if="i===2">每秒自动获得10%重置时获得的拝谣</span>
                <span v-else-if="i===3">每秒自动获得10%重置时获得的拞谤</span>
                <span v-else-if="i===4">每秒自动获得10%重置时获得的拟谥</span>
                <span v-else-if="i===5">自动购买拜谢升级且不消耗拜谢</span>
                <span v-else-if="i===6">自动购买拝谣升级且不消耗拝谣</span>
                <span v-else-if="i===7">自动购买拞谤升级且不消耗拞谤</span>
                <span v-else>自动购买拟谥升级且不消耗拟谥</span>
              </div>
              <div class="auto-cost">
                花费：{{ formatNumber([1e3,1e15,1e15,1e15,1e9,1e9,1e9,1e9][i-1]) }} {{ ['拜谢','拝谣','拞谤','拟谥','拜谢','拝谣','拞谤','拟谥'][i-1] }}
              </div>
            </button>
          </div>
        </div>
      </div>
      </div>
      
      
      <!-- 拠谦层级标签页 -->
      <div v-show="activeTab === 'juqian'" class="tab-content">
        <div class="section" v-if="isBcUnlocked">
          <h2>拠谦层级</h2>
        <div class="resources">
          <div class="resource">拠谦：<span class="counter">{{ formatNumber(game.bc) }}</span></div>
          <div class="resource">拠谦经验：<span class="counter">{{ formatNumber(game.bd) }}</span></div>
          <div class="resource">拠谦等级：<span class="counter">{{ game.bf }}</span></div>
          <div class="resource">拡谧：<span class="counter">{{ formatNumber(game.bg) }}</span></div>
          <div class="resource">拢谨：<span class="counter">{{ formatNumber(game.bh) }}</span></div>
          <div class="resource">拣谩：<span class="counter">{{ formatNumber(game.bi) }}</span></div>
        </div>
        <div class="actions" v-if="isBcUnlocked">
          <button @click="clickBc(1)" class="main-btn"><img src="/baixie.png" alt="" class="btn-icon" />拠谦一次 (获得 {{ formatNumber(getBcClickMultiplier()) }} 拠谦，{{ formatNumber(getBdClickMultiplier()) }} 经验)</button>
        </div>
        <div class="progress-bar" v-if="isBcUnlocked">
          <div class="progress-fill" :style="{ width: Math.min((game.bd / getBlUpgradeCost(game.bf)) * 100, 100) + '%' }">
            <span class="progress-text" style="position: absolute; left: 50%; transform: translateX(-50%);">{{ formatNumber(game.bd) }} / {{ formatNumber(getBlUpgradeCost(game.bf)) }}</span>
          </div>
        </div>
        <span class="bonus" v-if="isBcUnlocked">上一层资源加成：×{{ formatNumber(getBfBonus()) }}</span>
        <div class="reset-section" v-if="game.bf >= 40">
          <h3>重置系统</h3>
          <button @click="resetBc" class="reset-btn" :disabled="game.bf < 40">
            <img src="/baixie.png" alt="" class="btn-icon" />拤谪重置 (获得 {{ formatNumber(getBcResetReward()) }} BG)
          </button>
        </div>
        <div class="reset-section" v-if="game.bf >= 120">
          <button @click="resetBj" class="reset-btn" :disabled="game.bf < 120">
            <img src="/baixie.png" alt="" class="btn-icon" />拨谮重置 (获得 {{ formatNumber(getBjResetReward()) }} BJ)
          </button>
        </div>
        <div class="reset-section" v-if="game.bf >= 240">
          <button @click="resetBq" class="reset-btn" :disabled="game.bf < 240">
            <img src="/baixie.png" alt="" class="btn-icon" />拟谪重置 (获得 {{ formatNumber(getBqResetReward()) }} BQ)
          </button>
        </div>
        <div class="upgrades" v-if="isBcUnlocked">
          <h3>拠谦升级</h3>
          <button @click="upgradeBc(1)" :disabled="game.bc < getUpgradeCost(game.bcUpgrade1, 30, 1)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拠谦获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bcUpgrade1)) }} (Lv.{{ game.bcUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bcUpgrade1, 30, 1)) }} 拠谦
          </button>
          <button @click="upgradeBc(2)" :disabled="game.bc < getUpgradeCost(game.bcUpgrade2, 300, 2)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拠谦经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bcUpgrade2)) }} (Lv.{{ game.bcUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bcUpgrade2, 300, 2)) }} 拠谦
          </button>
          <button @click="upgradeBc(3)" :disabled="game.bc < getUpgradeCost(game.bcUpgrade3, 3000, 3)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拤谪获取 ×{{ formatNumber(getUpgrade3Effect(game.bcUpgrade3)) }} (Lv.{{ game.bcUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bcUpgrade3, 3000, 3)) }} 拠谦
          </button>
          <button @click="upgradeBc(4)" :disabled="game.bc < getUpgradeCost(game.bcUpgrade4, 30000, 4) || game.bcUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bcUpgrade4)) }} (Lv.{{ game.bcUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bcUpgrade4, 30000, 4)) }} 拠谦
          </button>
        </div>
        <div class="upgrades" v-if="game.bf >= 40 || game.bg > 0">
          <h3>拤谪升级</h3>
          <button @click="upgradeBn(1)" :disabled="game.bn < getUpgradeCost(game.bnUpgrade1, 10, 1)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拠谦获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bnUpgrade1)) }} (Lv.{{ game.bnUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bnUpgrade1, 10, 1)) }} BN
          </button>
          <button @click="upgradeBn(2)" :disabled="game.bn < getUpgradeCost(game.bnUpgrade2, 100, 2)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拠谦经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bnUpgrade2)) }} (Lv.{{ game.bnUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bnUpgrade2, 100, 2)) }} BN
          </button>
          <button @click="upgradeBn(3)" :disabled="game.bn < getUpgradeCost(game.bnUpgrade3, 1000, 3)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拨谮获取 ×{{ formatNumber(getUpgrade3Effect(game.bnUpgrade3)) }} (Lv.{{ game.bnUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bnUpgrade3, 1000, 3)) }} BN
          </button>
          <button @click="upgradeBn(4)" :disabled="game.bn < getUpgradeCost(game.bnUpgrade4, 10000, 4) || game.bnUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bnUpgrade4)) }} (Lv.{{ game.bnUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bnUpgrade4, 10000, 4)) }} BN
          </button>
        </div>
        <div class="upgrades" v-if="game.bf >= 120 || game.bh > 0">
          <h3>拨谮升级</h3>
          <button @click="upgradeBo(1)" :disabled="game.bo < getUpgradeCost(game.boUpgrade1, 10, 1)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拠谦获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.boUpgrade1)) }} (Lv.{{ game.boUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.boUpgrade1, 10, 1)) }} BO
          </button>
          <button @click="upgradeBo(2)" :disabled="game.bo < getUpgradeCost(game.boUpgrade2, 100, 2)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拠谦经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.boUpgrade2)) }} (Lv.{{ game.boUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.boUpgrade2, 100, 2)) }} BO
          </button>
          <button @click="upgradeBo(3)" :disabled="game.bo < getUpgradeCost(game.boUpgrade3, 1000, 3)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拟谪获取 ×{{ formatNumber(getUpgrade3Effect(game.boUpgrade3)) }} (Lv.{{ game.boUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.boUpgrade3, 1000, 3)) }} BO
          </button>
          <button @click="upgradeBo(4)" :disabled="game.bo < getUpgradeCost(game.boUpgrade4, 10000, 4) || game.boUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.boUpgrade4)) }} (Lv.{{ game.boUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.boUpgrade4, 10000, 4)) }} BO
          </button>
        </div>
        <div class="upgrades" v-if="game.bf >= 240 || game.bi > 0">
          <h3>拟谪升级</h3>
          <button @click="upgradeBp(1)" :disabled="game.bp < getUpgradeCost(game.bpUpgrade1, 10, 1)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拠谦获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bpUpgrade1)) }} (Lv.{{ game.bpUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bpUpgrade1, 10, 1)) }} BP
          </button>
          <button @click="upgradeBp(2)" :disabled="game.bp < getUpgradeCost(game.bpUpgrade2, 100, 2)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拠谦经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bpUpgrade2)) }} (Lv.{{ game.bpUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bpUpgrade2, 100, 2)) }} BP
          </button>
          <button @click="upgradeBp(3)" :disabled="game.bp < getUpgradeCost(game.bpUpgrade3, 1000, 3)" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拤谪获取 ×{{ formatNumber(getUpgrade3Effect(game.bpUpgrade3)) }} (Lv.{{ game.bpUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bpUpgrade3, 1000, 3)) }} BP
          </button>
          <button @click="upgradeBp(4)" :disabled="game.bp < getUpgradeCost(game.bpUpgrade4, 10000, 4) || game.bpUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bpUpgrade4)) }} (Lv.{{ game.bpUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bpUpgrade4, 10000, 4)) }} BP
          </button>
        </div>
        <div class="section">
          <h2>拠谦自动化</h2>
          <div class="auto-upgrades">
            <div v-for="i in 8" :key="i" class="auto-upgrade">
              <button @click="upgradeBcAuto(i)" :class="{ locked: !(game as any)[`bcAuto${i}Unlocked`] }" class="auto-btn">
                <img src="/baixie.png" alt="" class="btn-icon" />
                <div class="auto-title">{{ (game as any)[`bcAuto${i}Unlocked`] ? `自动化${i} 已解锁` : `自动化升级${i}` }}</div>
                <div class="auto-desc">
                  <span v-if="i===1">每 0.1 秒自动点击拠谦一次按钮</span>
                  <span v-else-if="i===2">每 0.1 秒自动获得拨赞重置</span>
                  <span v-else-if="i===3">每 0.1 秒自动获得拨赞重置</span>
                  <span v-else-if="i===4">每 0.1 秒自动获得拟谪重置</span>
                  <span v-else-if="i===5">自动购买 拠谦 升级</span>
                  <span v-else-if="i===6">自动购买 拤谪 升级</span>
                  <span v-else-if="i===7">自动购买 拨赞 升级</span>
                  <span v-else>自动购买 拟谪 升级</span>
                </div>
                <div class="auto-cost" v-if="!(game as any)[`bcAuto${i}Unlocked`]">
                  花费：{{ formatNumber([1e5,1e13,1e13,1e13,1e9,1e9,1e9,1e9][i-1]) }} {{ ['拠谦','拤谪','拨谮','拟谪','拠谦','拤谪','拨谮','拟谪'][i-1] }}
                </div>
              </button>
            </div>
          </div>
        </div>
      </div>
      </div>
      
      <!-- 拤谪层级标签页 -->
      <div v-show="activeTab === 'jiadi'" class="tab-content">
        <div class="section" v-if="isBjUnlocked">
          <h2>拤谪层级</h2>
          <div class="resources">
            <div class="resource">拤谪：<span class="counter">{{ formatNumber(game.bj) }}</span></div>
            <div class="resource">拤谪经验：<span class="counter">{{ formatNumber(game.bk) }}</span></div>
            <div class="resource">拤谪等级：<span class="counter">{{ game.bm }}</span></div>
            <div class="resource">拨谯：<span class="counter">{{ formatNumber(game.bn) }}</span></div>
            <div class="resource">拟谯：<span class="counter">{{ formatNumber(game.bo) }}</span></div>
            <div class="resource">设谯：<span class="counter">{{ formatNumber(game.bp) }}</span></div>
          </div>
          <div class="actions" v-if="isBjUnlocked">
            <button @click="clickBj(1)" class="main-btn"><img src="/baixie.png" alt="" class="btn-icon" />拤谪一次</button>
          </div>
        </div>
      </div>
      
      <!-- 拨谮层级标签页 -->
      <div v-show="activeTab === 'boxie'" class="tab-content">
        <div class="section" v-if="isBqUnlocked">
          <h2>拨谮层级</h2>
          <div class="resources">
            <div class="resource">拨谮：<span class="counter">{{ formatNumber(game.bq) }}</span></div>
            <div class="resource">拨谮经验：<span class="counter">{{ formatNumber(game.br) }}</span></div>
            <div class="resource">拨谮等级：<span class="counter">{{ game.bs }}</span></div>
            <div class="resource">设谮：<span class="counter">{{ formatNumber(game.bt) }}</span></div>
            <div class="resource">拟谮：<span class="counter">{{ formatNumber(game.bu) }}</span></div>
            <div class="resource">谮谯：<span class="counter">{{ formatNumber(game.bv) }}</span></div>
          </div>
          <div class="actions" v-if="isBqUnlocked">
            <button @click="clickBq(1)" class="main-btn"><img src="/baixie.png" alt="" class="btn-icon" />拨谮一次</button>
          </div>
        </div>
      </div>
      
      <!-- 拜谢之神标签页 -->
      <div v-show="activeTab === 'god'" class="tab-content">
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
      </div>
      
      <!-- 存档系统标签页 -->
      <div v-show="activeTab === 'save'" class="tab-content">
        <div class="section save-section">
          <h2>存档系统</h2>
          <div class="save-buttons">
            <button @click="exportSave" class="save-btn"><img src="/baixie.png" alt="" class="btn-icon" />导出存档</button>
            <label class="save-btn"><img src="/baixie.png" alt="" class="btn-icon" />导入存档<input type="file" @change="importSave" accept=".json" style="display: none" /></label>
            <button @click="hardReset" class="reset-btn danger"><img src="/baixie.png" alt="" class="btn-icon" />硬重置</button>
            <a href="https://www.bilibili.com/video/BV1GJ411x7h7" target="_blank" class="save-btn completion-btn"><img src="/baixie.png" alt="" class="btn-icon" />通关这个游戏</a>
          <span class="bonus">游戏说明：所有的升级1和2每10级效果乘以2，每100级效果乘以10，所有的升级3每10级效果乘以1.5，每100级效果乘以3.5。当前层级的等级到达400解锁下一层级。</span>
          </div>
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
.tab-navigation {
  display: flex;
  gap: 10px;
  justify-content: center;
  flex-wrap: wrap;
  margin-bottom: 30px;
  padding: 15px;
  background: rgba(255, 215, 0, 0.2);
  border-radius: 10px;
  border: 2px solid rgba(255, 215, 0, 0.5);
}
.tab-btn {
  padding: 10px 20px;
  font-size: 14px;
  background: linear-gradient(135deg, #ffd700 0%, #ffaa00 100%);
  color: #000;
  border: 2px solid #ffd700;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
  font-weight: bold;
}
.tab-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(255, 215, 0, 0.4);
}
.tab-btn.active {
  background: linear-gradient(135deg, #ffaa00 0%, #ffd700 100%);
  box-shadow: 0 0 20px rgba(255, 215, 0, 0.6);
  transform: translateY(-2px);
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
  margin-bottom: 15px;
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
  font-size: 1em;
  color: #000;
  margin: 10px;
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
  width: 32px;
  height: 32px;
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
  gap: 10px;
}
.auto-btn {
  width: 100%;
  padding: 15px;
  flex-direction: column;
  gap: 10px;
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
