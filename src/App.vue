<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch, markRaw } from 'vue'
import pako from 'pako'
import Decimal from 'break_infinity.js'

type DecimalSource = Decimal | number | string

const D = (value?: DecimalSource): Decimal => {
  if (value instanceof Decimal) return markRaw(value)
  return markRaw(new Decimal(value ?? 0))
}

const DECIMAL_FIELDS = new Set([
  'bx', 'be', 'by', 'bz', 'ba',
  'bc', 'bd', 'bg', 'bh', 'bi',
  'bj', 'bk', 'bn', 'bo', 'bp',
  'bq', 'br', 'bt', 'bu', 'bv', 'bw'
])

interface GameState {
  bx: Decimal; be: Decimal; bl: number; by: Decimal; bz: Decimal; ba: Decimal
  bxUpgrade1: number; bxUpgrade2: number; bxUpgrade3: number; bxUpgrade4: number
  byUpgrade1: number; byUpgrade2: number; byUpgrade3: number; byUpgrade4: number
  bzUpgrade1: number; bzUpgrade2: number; bzUpgrade3: number; bzUpgrade4: number
  baUpgrade1: number; baUpgrade2: number; baUpgrade3: number; baUpgrade4: number
  bc: Decimal; bd: Decimal; bf: number; bg: Decimal; bh: Decimal; bi: Decimal
  bcUpgrade1: number; bcUpgrade2: number; bcUpgrade3: number; bcUpgrade4: number
  bgUpgrade1: number; bgUpgrade2: number; bgUpgrade3: number; bgUpgrade4: number
  bhUpgrade1: number; bhUpgrade2: number; bhUpgrade3: number; bhUpgrade4: number
  biUpgrade1: number; biUpgrade2: number; biUpgrade3: number; biUpgrade4: number
  bj: Decimal; bk: Decimal; bm: number; bn: Decimal; bo: Decimal; bp: Decimal
  bjUpgrade1: number; bjUpgrade2: number; bjUpgrade3: number; bjUpgrade4: number
  bnUpgrade1: number; bnUpgrade2: number; bnUpgrade3: number; bnUpgrade4: number
  boUpgrade1: number; boUpgrade2: number; boUpgrade3: number; boUpgrade4: number
  bpUpgrade1: number; bpUpgrade2: number; bpUpgrade3: number; bpUpgrade4: number
  bq: Decimal; br: Decimal; bs: number; bt: Decimal; bu: Decimal; bv: Decimal
  bqUpgrade1: number; bqUpgrade2: number; bqUpgrade3: number; bqUpgrade4: number
  btUpgrade1: number; btUpgrade2: number; btUpgrade3: number; btUpgrade4: number
  buUpgrade1: number; buUpgrade2: number; buUpgrade3: number; buUpgrade4: number
  bvUpgrade1: number; bvUpgrade2: number; bvUpgrade3: number; bvUpgrade4: number
  bw: Decimal
  bxAuto1Unlocked: boolean; bxAuto2Unlocked: boolean; bxAuto3Unlocked: boolean; bxAuto4Unlocked: boolean
  bxAuto5Unlocked: boolean; bxAuto6Unlocked: boolean; bxAuto7Unlocked: boolean; bxAuto8Unlocked: boolean
  bcAuto1Unlocked: boolean; bcAuto2Unlocked: boolean; bcAuto3Unlocked: boolean; bcAuto4Unlocked: boolean
  bcAuto5Unlocked: boolean; bcAuto6Unlocked: boolean; bcAuto7Unlocked: boolean; bcAuto8Unlocked: boolean
  bjAuto1Unlocked: boolean; bjAuto2Unlocked: boolean; bjAuto3Unlocked: boolean; bjAuto4Unlocked: boolean
  bjAuto5Unlocked: boolean; bjAuto6Unlocked: boolean; bjAuto7Unlocked: boolean; bjAuto8Unlocked: boolean
  bqAuto1Unlocked: boolean; bqAuto2Unlocked: boolean; bqAuto3Unlocked: boolean; bqAuto4Unlocked: boolean
  bqAuto5Unlocked: boolean; bqAuto6Unlocked: boolean; bqAuto7Unlocked: boolean; bqAuto8Unlocked: boolean
  isBcUnlocked: boolean; isBjUnlocked: boolean; isBqUnlocked: boolean; isBwUnlocked: boolean
  gameEnded: boolean
}

const defaultSave: GameState = {
  bx: D(0), be: D(0), bl: 1, by: D(0), bz: D(0), ba: D(0),
  bxUpgrade1: 0, bxUpgrade2: 0, bxUpgrade3: 0, bxUpgrade4: 0,
  byUpgrade1: 0, byUpgrade2: 0, byUpgrade3: 0, byUpgrade4: 0,
  bzUpgrade1: 0, bzUpgrade2: 0, bzUpgrade3: 0, bzUpgrade4: 0,
  baUpgrade1: 0, baUpgrade2: 0, baUpgrade3: 0, baUpgrade4: 0,
  bc: D(0), bd: D(0), bf: 1, bg: D(0), bh: D(0), bi: D(0),
  bcUpgrade1: 0, bcUpgrade2: 0, bcUpgrade3: 0, bcUpgrade4: 0,
  bgUpgrade1: 0, bgUpgrade2: 0, bgUpgrade3: 0, bgUpgrade4: 0,
  bhUpgrade1: 0, bhUpgrade2: 0, bhUpgrade3: 0, bhUpgrade4: 0,
  biUpgrade1: 0, biUpgrade2: 0, biUpgrade3: 0, biUpgrade4: 0,
  bj: D(0), bk: D(0), bm: 1, bn: D(0), bo: D(0), bp: D(0),
  bjUpgrade1: 0, bjUpgrade2: 0, bjUpgrade3: 0, bjUpgrade4: 0,
  bnUpgrade1: 0, bnUpgrade2: 0, bnUpgrade3: 0, bnUpgrade4: 0,
  boUpgrade1: 0, boUpgrade2: 0, boUpgrade3: 0, boUpgrade4: 0,
  bpUpgrade1: 0, bpUpgrade2: 0, bpUpgrade3: 0, bpUpgrade4: 0,
  bq: D(0), br: D(0), bs: 1, bt: D(0), bu: D(0), bv: D(0),
  bqUpgrade1: 0, bqUpgrade2: 0, bqUpgrade3: 0, bqUpgrade4: 0,
  btUpgrade1: 0, btUpgrade2: 0, btUpgrade3: 0, btUpgrade4: 0,
  buUpgrade1: 0, buUpgrade2: 0, buUpgrade3: 0, buUpgrade4: 0,
  bvUpgrade1: 0, bvUpgrade2: 0, bvUpgrade3: 0, bvUpgrade4: 0,
  bw: D(0),
  bxAuto1Unlocked: false, bxAuto2Unlocked: false, bxAuto3Unlocked: false, bxAuto4Unlocked: false,
  bxAuto5Unlocked: false, bxAuto6Unlocked: false, bxAuto7Unlocked: false, bxAuto8Unlocked: false,
  bcAuto1Unlocked: false, bcAuto2Unlocked: false, bcAuto3Unlocked: false, bcAuto4Unlocked: false,
  bcAuto5Unlocked: false, bcAuto6Unlocked: false, bcAuto7Unlocked: false, bcAuto8Unlocked: false,
  bjAuto1Unlocked: false, bjAuto2Unlocked: false, bjAuto3Unlocked: false, bjAuto4Unlocked: false,
  bjAuto5Unlocked: false, bjAuto6Unlocked: false, bjAuto7Unlocked: false, bjAuto8Unlocked: false,
  bqAuto1Unlocked: false, bqAuto2Unlocked: false, bqAuto3Unlocked: false, bqAuto4Unlocked: false,
  bqAuto5Unlocked: false, bqAuto6Unlocked: false, bqAuto7Unlocked: false, bqAuto8Unlocked: false,
  isBcUnlocked: false, isBjUnlocked: false, isBqUnlocked: false, isBwUnlocked: false,
  gameEnded: false
}

const game = ref<GameState>({ ...defaultSave })

const activeTab = ref('baixie')
const showBgImage = ref(true)

const formatNumber = (num: Decimal | number): string => {
  const d = num instanceof Decimal ? num : new Decimal(num)
  if (d.eq(0)) return '0'
  if (!Number.isFinite(d.mantissa) || !Number.isFinite(d.exponent)) return '拜谢'
  if (d.lt(1000)) return d.toFixed(1)
  return `${d.mantissa.toFixed(3)}e${d.exponent}`
}

// 升级花费乘数常量
const UPGRADE_COST_MULTIPLIER_1_3 = 1.25
const UPGRADE_COST_MULTIPLIER_4 = 10

// 升级效果乘数常量
const UPGRADE_1_OR_2_HUNDREDS_MULTIPLIER = 10
const UPGRADE_1_OR_2_TENS_MULTIPLIER = 2
const UPGRADE_3_HUNDREDS_MULTIPLIER = 3.1622
const UPGRADE_3_TENS_MULTIPLIER = 1.4142

// 升级花费计算
const getUpgradeCost = (level: number, baseCost: number, upgradeType: number): Decimal => {
  const multiplier = upgradeType === 4 ? UPGRADE_COST_MULTIPLIER_4 : UPGRADE_COST_MULTIPLIER_1_3
  return D(baseCost).mul(Decimal.pow(multiplier, level)).floor()
}

// 升级经验需求计算
const getBlUpgradeCost = (bl: number): Decimal => bl === 0 ? D(1) : D(40).mul(bl).mul(Decimal.pow(1.25, bl))
const getBfUpgradeCost = (bf: number): Decimal => bf === 0 ? D(1) : D(40).mul(bf).mul(Decimal.pow(1.25, bf))
const getBmUpgradeCost = (bm: number): Decimal => bm === 0 ? D(1) : D(40).mul(bm).mul(Decimal.pow(1.25, bm))
const getBsUpgradeCost = (bs: number): Decimal => bs === 0 ? D(1) : D(40).mul(bs).mul(Decimal.pow(1.25, bs))

// 升级效果计算
const getUpgrade1Or2Effect = (level: number): Decimal => {
  let effect = D(level)
  const hundreds = Math.floor(level / 100)
  const tens = Math.floor(level / 10)
  effect = effect.mul(Decimal.pow(UPGRADE_1_OR_2_HUNDREDS_MULTIPLIER, hundreds))
  effect = effect.mul(Decimal.pow(UPGRADE_1_OR_2_TENS_MULTIPLIER, tens))
  return D(1).add(effect)
}
const getUpgrade3Effect = (level: number): Decimal => {
  let effect = D(level).mul(0.1)
  const hundreds = Math.floor(level / 100)
  const tens = Math.floor(level / 10)
  effect = effect.mul(Decimal.pow(UPGRADE_3_HUNDREDS_MULTIPLIER, hundreds))
  effect = effect.mul(Decimal.pow(UPGRADE_3_TENS_MULTIPLIER, tens))
  return D(1).add(effect)
}
const getUpgrade4Effect = (level: number): number => 1 + Math.min(level, 9)

// 等级奖励计算
const getLevelBonus = (level: number): Decimal => {
  return level === 0 ? D(1) : D(level).mul(Decimal.pow(1.05, level)).floor()
}

// 重置获得的资源计算
const getByResetReward = (): Decimal => game.value.bl < 40 ? D(0) : D(game.value.bl / 10).mul(Decimal.pow(1.04, game.value.bl - 40)).mul(getUpgrade3Effect(game.value.bxUpgrade3)).mul(getLevelBonus(game.value.bf)).mul(getBwBonus())
const getBzResetReward = (): Decimal => game.value.bl < 120 ? D(0) : D(game.value.bl / 10).mul(Decimal.pow(1.03, game.value.bl - 120)).mul(getUpgrade3Effect(game.value.byUpgrade3)).mul(getLevelBonus(game.value.bf)).mul(getBwBonus())
const getBaResetReward = (): Decimal => game.value.bl < 240 ? D(0) : D(game.value.bl / 10).mul(Decimal.pow(1.02, game.value.bl - 240)).mul(getUpgrade3Effect(game.value.bzUpgrade3)).mul(getLevelBonus(game.value.bf)).mul(getBwBonus())
const getBcResetReward = (): Decimal => game.value.bl < 400 ? D(0) : getUpgrade3Effect(game.value.baUpgrade3).mul(getLevelBonus(game.value.bm)).mul(getBwBonus())

const getBgResetReward = (): Decimal => game.value.bf < 40 ? D(0) : D(game.value.bf / 10).mul(Decimal.pow(1.04, game.value.bf - 40)).mul(getUpgrade3Effect(game.value.bcUpgrade3)).mul(getLevelBonus(game.value.bm)).mul(getBwBonus())
const getBhResetReward = (): Decimal => game.value.bf < 120 ? D(0) : D(game.value.bf / 10).mul(Decimal.pow(1.03, game.value.bf - 120)).mul(getUpgrade3Effect(game.value.bgUpgrade3)).mul(getLevelBonus(game.value.bm)).mul(getBwBonus())
const getBiResetReward = (): Decimal => game.value.bf < 240 ? D(0) : D(game.value.bf / 10).mul(Decimal.pow(1.02, game.value.bf - 240)).mul(getUpgrade3Effect(game.value.bhUpgrade3)).mul(getLevelBonus(game.value.bm)).mul(getBwBonus())
const getBjResetReward = (): Decimal => game.value.bf < 400 ? D(0) : getUpgrade3Effect(game.value.bjUpgrade3).mul(getLevelBonus(game.value.bs)).mul(getBwBonus())

const getBnResetReward = (): Decimal => game.value.bm < 40 ? D(0) : D(game.value.bm / 10).mul(Decimal.pow(1.04, game.value.bm - 40)).mul(getUpgrade3Effect(game.value.bnUpgrade3)).mul(getLevelBonus(game.value.bs)).mul(getBwBonus())
const getBoResetReward = (): Decimal => game.value.bm < 120 ? D(0) : D(game.value.bm / 10).mul(Decimal.pow(1.03, game.value.bm - 120)).mul(getUpgrade3Effect(game.value.boUpgrade3)).mul(getLevelBonus(game.value.bs)).mul(getBwBonus())
const getBpResetReward = (): Decimal => game.value.bm < 240 ? D(0) : D(game.value.bm / 10).mul(Decimal.pow(1.02, game.value.bm - 240)).mul(getUpgrade3Effect(game.value.bpUpgrade3)).mul(getLevelBonus(game.value.bs)).mul(getBwBonus())
const getBqResetReward = (): Decimal => game.value.bm < 400 ? D(0) : getUpgrade3Effect(game.value.bpUpgrade3).mul(getBwBonus())

const getBtResetReward = (): Decimal => game.value.bs < 40 ? D(0) : D(game.value.bs / 10).mul(Decimal.pow(1.04, game.value.bs - 40)).mul(getUpgrade3Effect(game.value.bqUpgrade3)).mul(getBwBonus())
const getBuResetReward = (): Decimal => game.value.bs < 120 ? D(0) : D(game.value.bs / 10).mul(Decimal.pow(1.03, game.value.bs - 120)).mul(getUpgrade3Effect(game.value.btUpgrade3)).mul(getBwBonus())
const getBvResetReward = (): Decimal => game.value.bs < 240 ? D(0) : D(game.value.bs / 10).mul(Decimal.pow(1.02, game.value.bs - 240)).mul(getUpgrade3Effect(game.value.buUpgrade3)).mul(getBwBonus())
const getBwResetReward = (): Decimal => game.value.bs < 400 ? D(0) : D(game.value.bs / 100).mul(Decimal.pow(1.01, game.value.bs - 400)).mul(getUpgrade3Effect(game.value.bvUpgrade3))

const checkUnlocks = () => {
  if (game.value.bl >= 400) game.value.isBcUnlocked = true
  if (game.value.bf >= 400) game.value.isBjUnlocked = true
  if (game.value.bm >= 400) game.value.isBqUnlocked = true
  if (game.value.bs >= 400) game.value.isBwUnlocked = true
}
const getBwBonus = (): Decimal => game.value.bw.add(1)

// 点击倍率计算
const getBxClickMultiplier = (): Decimal => {
  return getUpgrade1Or2Effect(game.value.bxUpgrade1).mul(getUpgrade1Or2Effect(game.value.byUpgrade1)).mul(getUpgrade1Or2Effect(game.value.bzUpgrade1)).mul(getUpgrade1Or2Effect(game.value.baUpgrade1)).mul(getLevelBonus(game.value.bl)).mul(getLevelBonus(game.value.bf)).mul(getBwBonus())
}

const getBeClickMultiplier = (): Decimal => {
  return getUpgrade1Or2Effect(game.value.bxUpgrade2).mul(getUpgrade1Or2Effect(game.value.byUpgrade2)).mul(getUpgrade1Or2Effect(game.value.bzUpgrade2)).mul(getUpgrade1Or2Effect(game.value.baUpgrade2)).mul(getLevelBonus(game.value.bf)).mul(getBwBonus())
}

const getBcClickMultiplier = (): Decimal => {
  return D(100).mul(getBcResetReward()).mul(getUpgrade1Or2Effect(game.value.bcUpgrade1)).mul(getUpgrade1Or2Effect(game.value.bgUpgrade1)).mul(getUpgrade1Or2Effect(game.value.bhUpgrade1)).mul(getUpgrade1Or2Effect(game.value.biUpgrade1)).mul(getLevelBonus(game.value.bm))
}

const getBdClickMultiplier = (): Decimal => {
  return D(100).mul(getUpgrade1Or2Effect(game.value.bcUpgrade2)).mul(getUpgrade1Or2Effect(game.value.bgUpgrade2)).mul(getUpgrade1Or2Effect(game.value.bhUpgrade2)).mul(getUpgrade1Or2Effect(game.value.biUpgrade2)).mul(getLevelBonus(game.value.bm))
}

const getBjClickMultiplier = (): Decimal => {
  return D(100).mul(getBjResetReward()).mul(getUpgrade1Or2Effect(game.value.bjUpgrade1)).mul(getUpgrade1Or2Effect(game.value.bnUpgrade1)).mul(getUpgrade1Or2Effect(game.value.boUpgrade1)).mul(getUpgrade1Or2Effect(game.value.bpUpgrade1)).mul(getLevelBonus(game.value.bs))
}

const getBkClickMultiplier = (): Decimal => {
  return D(100).mul(getUpgrade1Or2Effect(game.value.bjUpgrade2)).mul(getUpgrade1Or2Effect(game.value.bnUpgrade2)).mul(getUpgrade1Or2Effect(game.value.boUpgrade2)).mul(getUpgrade1Or2Effect(game.value.bpUpgrade2)).mul(getLevelBonus(game.value.bs))
}

const getBqClickMultiplier = (): Decimal => {
  return D(100).mul(getBqResetReward()).mul(getUpgrade1Or2Effect(game.value.bqUpgrade1)).mul(getUpgrade1Or2Effect(game.value.btUpgrade1)).mul(getUpgrade1Or2Effect(game.value.buUpgrade1)).mul(getUpgrade1Or2Effect(game.value.bvUpgrade1))
}

const getBrClickMultiplier = (): Decimal => {
  return D(100).mul(getUpgrade1Or2Effect(game.value.bqUpgrade2)).mul(getUpgrade1Or2Effect(game.value.btUpgrade2)).mul(getUpgrade1Or2Effect(game.value.buUpgrade2)).mul(getUpgrade1Or2Effect(game.value.bvUpgrade2))
}

const autoUnlocked = (i: number): boolean => {
  const key = `bxAuto${i}Unlocked` as keyof GameState
  return game.value[key] as boolean
}

// 拜谢层级计算
const clickBx = (clickMultiplier: number = 1) => {
  if (game.value.gameEnded) return
  game.value.bx = D(game.value.bx.add(getBxClickMultiplier().mul(clickMultiplier)))
  game.value.be = D(game.value.be.add(getBeClickMultiplier().mul(clickMultiplier)))
}

const resetBy = () => {
  if (game.value.bl < 40) return
  game.value.by = D(game.value.by.add(getByResetReward()))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
}

const resetBz = () => {
  if (game.value.bl < 120) return
  game.value.bz = D(game.value.bz.add(getBzResetReward()))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0)
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
}

const resetBa = () => {
  if (game.value.bl < 240) return
  game.value.ba = D(game.value.ba.add(getBaResetReward()))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0); game.value.bz = D(0)
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
}

const autoUpgradeBl = () => {
  if (game.value.gameEnded) return
  let cost = getBlUpgradeCost(game.value.bl)
  while (game.value.be.gte(cost) && cost.gt(0)) {
    game.value.be = D(game.value.be.sub(cost))
    game.value.bl++
    cost = getBlUpgradeCost(game.value.bl)
  }
}

const upgradeBx = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bxUpgrade1, 10, 1)
      if (game.value.bx.gte(cost)) { game.value.bx = D(game.value.bx.sub(cost)); game.value.bxUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bxUpgrade2, 100, 2)
      if (game.value.bx.gte(cost)) { game.value.bx = D(game.value.bx.sub(cost)); game.value.bxUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bxUpgrade3, 1000, 3)
      if (game.value.bx.gte(cost)) { game.value.bx = D(game.value.bx.sub(cost)); game.value.bxUpgrade3++ }
      break
    case 4:
      if (game.value.bxUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bxUpgrade4, 10000, 4)
        if (game.value.bx.gte(cost)) { game.value.bx = D(game.value.bx.sub(cost)); game.value.bxUpgrade4++ }
      }
      break
  }
}

const upgradeBy = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.byUpgrade1, 10, 1)
      if (game.value.by.gte(cost)) { game.value.by = D(game.value.by.sub(cost)); game.value.byUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.byUpgrade2, 100, 2)
      if (game.value.by.gte(cost)) { game.value.by = D(game.value.by.sub(cost)); game.value.byUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.byUpgrade3, 1000, 3)
      if (game.value.by.gte(cost)) { game.value.by = D(game.value.by.sub(cost)); game.value.byUpgrade3++ }
      break
    case 4:
      if (game.value.byUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.byUpgrade4, 10000, 4)
        if (game.value.by.gte(cost)) { game.value.by = D(game.value.by.sub(cost)); game.value.byUpgrade4++ }
      }
      break
  }
}

const upgradeBz = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bzUpgrade1, 10, 1)
      if (game.value.bz.gte(cost)) { game.value.bz = D(game.value.bz.sub(cost)); game.value.bzUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bzUpgrade2, 100, 2)
      if (game.value.bz.gte(cost)) { game.value.bz = D(game.value.bz.sub(cost)); game.value.bzUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bzUpgrade3, 1000, 3)
      if (game.value.bz.gte(cost)) { game.value.bz = D(game.value.bz.sub(cost)); game.value.bzUpgrade3++ }
      break
    case 4:
      if (game.value.bzUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bzUpgrade4, 10000, 4)
        if (game.value.bz.gte(cost)) { game.value.bz = D(game.value.bz.sub(cost)); game.value.bzUpgrade4++ }
      }
      break
  }
}

const upgradeBa = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.baUpgrade1, 10, 1)
      if (game.value.ba.gte(cost)) { game.value.ba = D(game.value.ba.sub(cost)); game.value.baUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.baUpgrade2, 100, 2)
      if (game.value.ba.gte(cost)) { game.value.ba = D(game.value.ba.sub(cost)); game.value.baUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.baUpgrade3, 1000, 3)
      if (game.value.ba.gte(cost)) { game.value.ba = D(game.value.ba.sub(cost)); game.value.baUpgrade3++ }
      break
    case 4:
      if (game.value.baUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.baUpgrade4, 10000, 4)
        if (game.value.ba.gte(cost)) { game.value.ba = D(game.value.ba.sub(cost)); game.value.baUpgrade4++ }
      }
      break
  }
}

// 拜谢层级自动化
const upgradeBxAuto = (autoIndex: number) => {
  if (game.value.gameEnded) return
  const autoKey = `bxAuto${autoIndex}Unlocked` as keyof GameState
  const isUnlocked = game.value[autoKey] as unknown as boolean
  const unlockCosts = [1e3, 1e15, 1e15, 1e15, 1e9, 1e9, 1e9, 1e9]
  const unlockResources = [game.value.bx, game.value.by, game.value.bz, game.value.ba, game.value.bx, game.value.by, game.value.bz, game.value.ba]
  if (!isUnlocked) {
    if (unlockResources[autoIndex - 1].gte(unlockCosts[autoIndex - 1])) {
      ;(game.value[autoKey] as unknown as boolean) = true
    }
  }
}

const autoClickBx = () => {
  if (!game.value.bxAuto1Unlocked) return
  const mult = getUpgrade4Effect(game.value.bxUpgrade4) * getUpgrade4Effect(game.value.byUpgrade4) * getUpgrade4Effect(game.value.bzUpgrade4) * getUpgrade4Effect(game.value.baUpgrade4)
  * getUpgrade4Effect(game.value.bcUpgrade4) * getUpgrade4Effect(game.value.bgUpgrade4) * getUpgrade4Effect(game.value.bhUpgrade4) * getUpgrade4Effect(game.value.biUpgrade4)
  * getUpgrade4Effect(game.value.bjUpgrade4) * getUpgrade4Effect(game.value.bnUpgrade4) * getUpgrade4Effect(game.value.boUpgrade4) * getUpgrade4Effect(game.value.bpUpgrade4)
  * getUpgrade4Effect(game.value.bqUpgrade4) * getUpgrade4Effect(game.value.btUpgrade4) * getUpgrade4Effect(game.value.buUpgrade4) * getUpgrade4Effect(game.value.bvUpgrade4)
  clickBx(mult/10)
}

const autoGetResetResources = () => {
  if (game.value.bxAuto2Unlocked) {
    game.value.by = D(game.value.by.add(getByResetReward().mul(getBwBonus()).div(100)))
  }
  if (game.value.bxAuto3Unlocked) {
    game.value.bz = D(game.value.bz.add(getBzResetReward().mul(getBwBonus()).div(100)))
  }
  if (game.value.bxAuto4Unlocked) {
    game.value.ba = D(game.value.ba.add(getBaResetReward().mul(getBwBonus()).div(100)))
  }
}

const calculateMaxPurchases = (resource: Decimal, baseCost: number, multiplier: number, currentLevel: number): number => {
  const firstTermCost = D(baseCost).mul(Decimal.pow(multiplier, currentLevel))
  if (resource.lt(firstTermCost)) return 0
  const r = multiplier
  const lnMaxN = D(1).add(resource.mul(r - 1).div(firstTermCost)).ln()
  const lnR = new Decimal(r).ln()
  const maxN = lnMaxN / lnR
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
          case 3: game.value.bxUpgrade4 = Math.min(Math.floor(game.value.bx.div(1000).add(1).log10()), 9); break
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
          case 3: game.value.byUpgrade4 = Math.min(Math.floor(game.value.by.div(1000).add(1).log10()), 9); break
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
          case 3: game.value.bzUpgrade4 = Math.min(Math.floor(game.value.bz.div(1000).add(1).log10()), 9); break
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
          case 3: game.value.baUpgrade4 = Math.min(Math.floor(game.value.ba.div(1000).add(1).log10()), 9); break
        }
      }
    })
  }
}

// 拠谦层级计算
const clickBc = (clickMultiplier: number = 1) => {
  if (game.value.bl < 400) return
  game.value.bc = D(game.value.bc.add(getBcClickMultiplier().mul(clickMultiplier)))
  game.value.bd = D(game.value.bd.add(getBdClickMultiplier().mul(clickMultiplier)))
  //重置拜谢层级除自动化外的所有内容
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0); game.value.bz = D(0); game.value.ba = D(0)
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
  game.value.baUpgrade1 = 0; game.value.baUpgrade2 = 0; game.value.baUpgrade3 = 0; game.value.baUpgrade4 = 0
}

const resetBg = () => {
  if (game.value.bf < 40) return
  game.value.bg = D(game.value.bg.add(getBgResetReward()))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0); game.value.bz = D(0); game.value.ba = D(0)
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
  game.value.baUpgrade1 = 0; game.value.baUpgrade2 = 0; game.value.baUpgrade3 = 0; game.value.baUpgrade4 = 0
  game.value.bc = D(0); game.value.bd = D(0); game.value.bf = 1
  game.value.bcUpgrade1 = 0; game.value.bcUpgrade2 = 0; game.value.bcUpgrade3 = 0; game.value.bcUpgrade4 = 0
}

const resetBh = () => {
  if (game.value.bf < 120) return
  game.value.bh = D(game.value.bh.add(getBhResetReward()))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0); game.value.bz = D(0); game.value.ba = D(0)
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
  game.value.baUpgrade1 = 0; game.value.baUpgrade2 = 0; game.value.baUpgrade3 = 0; game.value.baUpgrade4 = 0
  game.value.bc = D(0); game.value.bd = D(0); game.value.bf = 1; game.value.bg = D(0)
  game.value.bcUpgrade1 = 0; game.value.bcUpgrade2 = 0; game.value.bcUpgrade3 = 0; game.value.bcUpgrade4 = 0
  game.value.bgUpgrade1 = 0; game.value.bgUpgrade2 = 0; game.value.bgUpgrade3 = 0; game.value.bgUpgrade4 = 0
}

const resetBi = () => {
  if (game.value.bf < 120) return
  game.value.bi = D(game.value.bi.add(getBiResetReward()))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0); game.value.bz = D(0); game.value.ba = D(0)
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
  game.value.baUpgrade1 = 0; game.value.baUpgrade2 = 0; game.value.baUpgrade3 = 0; game.value.baUpgrade4 = 0
  game.value.bc = D(0); game.value.bd = D(0); game.value.bf = 1; game.value.bg = D(0); game.value.bh = D(0)
  game.value.bcUpgrade1 = 0; game.value.bcUpgrade2 = 0; game.value.bcUpgrade3 = 0; game.value.bcUpgrade4 = 0
  game.value.bgUpgrade1 = 0; game.value.bgUpgrade2 = 0; game.value.bgUpgrade3 = 0; game.value.bgUpgrade4 = 0
  game.value.bhUpgrade1 = 0; game.value.bhUpgrade2 = 0; game.value.bhUpgrade3 = 0; game.value.bhUpgrade4 = 0
}

const autoUpgradeBf = () => {
  if (game.value.gameEnded) return
  let cost = getBfUpgradeCost(game.value.bf)
  while (game.value.bd.gte(cost) && cost.gt(0)) {
    game.value.bd = D(game.value.bd.sub(cost))
    game.value.bf++
    cost = getBfUpgradeCost(game.value.bf)
  }
}


const upgradeBc = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bcUpgrade1, 30, 1)
      if (game.value.bc.gte(cost)) { game.value.bc = D(game.value.bc.sub(cost)); game.value.bcUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bcUpgrade2, 300, 2)
      if (game.value.bc.gte(cost)) { game.value.bc = D(game.value.bc.sub(cost)); game.value.bcUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bcUpgrade3, 3000, 3)
      if (game.value.bc.gte(cost)) { game.value.bc = D(game.value.bc.sub(cost)); game.value.bcUpgrade3++ }
      break
    case 4:
      if (game.value.bcUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bcUpgrade4, 30000, 4)
        if (game.value.bc.gte(cost)) { game.value.bc = D(game.value.bc.sub(cost)); game.value.bcUpgrade4++ }
      }
      break
  }
}


const upgradeBg = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bgUpgrade1, 30, 1)
      if (game.value.bg.gte(cost)) { game.value.bg = D(game.value.bg.sub(cost)); game.value.bgUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bgUpgrade2, 300, 2)
      if (game.value.bg.gte(cost)) { game.value.bg = D(game.value.bg.sub(cost)); game.value.bgUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bgUpgrade3, 3000, 3)
      if (game.value.bg.gte(cost)) { game.value.bg = D(game.value.bg.sub(cost)); game.value.bgUpgrade3++ }
      break
    case 4:
      if (game.value.bgUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bgUpgrade4, 30000, 4)
        if (game.value.bg.gte(cost)) { game.value.bg = D(game.value.bg.sub(cost)); game.value.bgUpgrade4++ }
      }
      break
  }
}

const upgradeBh = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bhUpgrade1, 30, 1)
      if (game.value.bh.gte(cost)) { game.value.bh = D(game.value.bh.sub(cost)); game.value.bhUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bhUpgrade2, 300, 2)
      if (game.value.bh.gte(cost)) { game.value.bh = D(game.value.bh.sub(cost)); game.value.bhUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bhUpgrade3, 3000, 3)
      if (game.value.bh.gte(cost)) { game.value.bh = D(game.value.bh.sub(cost)); game.value.bhUpgrade3++ }
      break
    case 4:
      if (game.value.bhUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bhUpgrade4, 30000, 4)
        if (game.value.bh.gte(cost)) { game.value.bh = D(game.value.bh.sub(cost)); game.value.bhUpgrade4++ }
      }
      break
  }
}


const upgradeBi = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.biUpgrade1, 30, 1)
      if (game.value.bi.gte(cost)) { game.value.bi = D(game.value.bi.sub(cost)); game.value.biUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.biUpgrade2, 300, 2)
      if (game.value.bi.gte(cost)) { game.value.bi = D(game.value.bi.sub(cost)); game.value.biUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.biUpgrade3, 3000, 3)
      if (game.value.bi.gte(cost)) { game.value.bi = D(game.value.bi.sub(cost)); game.value.biUpgrade3++ }
      break
    case 4:
      if (game.value.biUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.biUpgrade4, 30000, 4)
        if (game.value.bi.gte(cost)) { game.value.bi = D(game.value.bi.sub(cost)); game.value.biUpgrade4++ }
      }
      break
  }
}

// 拠谦层级自动化
const upgradeBcAuto = (autoIndex: number) => {
  if (game.value.gameEnded) return
  const autoKey = `bcAuto${autoIndex}Unlocked` as keyof GameState
  const isUnlocked = game.value[autoKey] as unknown as boolean
  const unlockCosts = [1e16, 1e16, 1e16, 1e16, 1e10, 1e10, 1e10, 1e10]
  const unlockResources = [game.value.bc, game.value.bg, game.value.bh, game.value.bi, game.value.bc, game.value.bg, game.value.bh, game.value.bi]
  if (!isUnlocked) {
    if (unlockResources[autoIndex - 1].gte(unlockCosts[autoIndex - 1])) {
      ;(game.value[autoKey] as unknown as boolean) = true
    }
  }
}

const autoGetBcResetResources = () => {
  if (game.value.bcAuto1Unlocked) {
    game.value.bc = D(game.value.bc.add(getBcClickMultiplier().div(10)))
    game.value.bd = D(game.value.bd.add(getBdClickMultiplier().div(10)))
  }
  if (game.value.bcAuto2Unlocked) {
    game.value.bg = D(game.value.bg.add(getBgResetReward().div(10)))
  }
  if (game.value.bcAuto3Unlocked) {
    game.value.bh = D(game.value.bh.add(getBhResetReward().div(10)))
  }
  if (game.value.bcAuto4Unlocked) {
    game.value.bi = D(game.value.bi.add(getBiResetReward().div(10)))
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
          case 3: game.value.bcUpgrade4 = Math.min(Math.floor(game.value.bc.div(3000).add(1).log10()), 9); break
        }
      }
    })
  }
  if (game.value.bcAuto6Unlocked) {
    const upgrades = [
      { level: game.value.bgUpgrade1, base: 30 },
      { level: game.value.bgUpgrade2, base: 300 },
      { level: game.value.bgUpgrade3, base: 3000 },
      { level: game.value.bgUpgrade4, base: 30000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bg, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.bgUpgrade1 += maxBuy; break
          case 1: game.value.bgUpgrade2 += maxBuy; break
          case 2: game.value.bgUpgrade3 += maxBuy; break
          case 3: game.value.bgUpgrade4 = Math.min(Math.floor(game.value.bg.div(3000).add(1).log10()), 9); break
        }
      }
    })
  }
  if (game.value.bcAuto7Unlocked) {
    const upgrades = [
      { level: game.value.bhUpgrade1, base: 30 },
      { level: game.value.bhUpgrade2, base: 300 },
      { level: game.value.bhUpgrade3, base: 3000 },
      { level: game.value.bhUpgrade4, base: 30000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bh, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.bhUpgrade1 += maxBuy; break
          case 1: game.value.bhUpgrade2 += maxBuy; break
          case 2: game.value.bhUpgrade3 += maxBuy; break
          case 3: game.value.bhUpgrade4 = Math.min(Math.floor(game.value.bh.div(3000).add(1).log10()), 9); break
        }
      }
    })
  }
  if (game.value.bcAuto8Unlocked) {
    const upgrades = [
      { level: game.value.biUpgrade1, base: 30 },
      { level: game.value.biUpgrade2, base: 300 },
      { level: game.value.biUpgrade3, base: 3000 },
      { level: game.value.biUpgrade4, base: 30000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bi, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.biUpgrade1 += maxBuy; break
          case 1: game.value.biUpgrade2 += maxBuy; break
          case 2: game.value.biUpgrade3 += maxBuy; break
          case 3: game.value.biUpgrade4 = Math.min(Math.floor(game.value.bi.div(3000).add(1).log10()), 9); break
        }
      }
    })
  }
}

// 拤谪层级计算
const clickBj = (clickMultiplier: number = 1) => {
  if (game.value.bf < 400) return
  const bjMultiplier = getUpgrade1Or2Effect(game.value.bjUpgrade1).mul(getUpgrade1Or2Effect(game.value.btUpgrade1)).mul(getUpgrade1Or2Effect(game.value.buUpgrade1)).mul(getUpgrade1Or2Effect(game.value.bvUpgrade1)).mul(getBwBonus())
  const bkMultiplier = getUpgrade1Or2Effect(game.value.bjUpgrade2).mul(getUpgrade1Or2Effect(game.value.btUpgrade2)).mul(getUpgrade1Or2Effect(game.value.buUpgrade2)).mul(getUpgrade1Or2Effect(game.value.bvUpgrade2)).mul(getBwBonus())
  game.value.bj = D(game.value.bj.add(bjMultiplier.mul(clickMultiplier)))
  game.value.bk = D(game.value.bk.add(bkMultiplier.mul(clickMultiplier)))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0); game.value.bz = D(0); game.value.ba = D(0)
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
  game.value.baUpgrade1 = 0; game.value.baUpgrade2 = 0; game.value.baUpgrade3 = 0; game.value.baUpgrade4 = 0
  game.value.bc = D(0); game.value.bd = D(0); game.value.bf = 1; game.value.bg = D(0); game.value.bh = D(0); game.value.bi = D(0)
  game.value.bcUpgrade1 = 0; game.value.bcUpgrade2 = 0; game.value.bcUpgrade3 = 0; game.value.bcUpgrade4 = 0
  game.value.bgUpgrade1 = 0; game.value.bgUpgrade2 = 0; game.value.bgUpgrade3 = 0; game.value.bgUpgrade4 = 0
  game.value.bhUpgrade1 = 0; game.value.bhUpgrade2 = 0; game.value.bhUpgrade3 = 0; game.value.bhUpgrade4 = 0
  game.value.biUpgrade1 = 0; game.value.biUpgrade2 = 0; game.value.biUpgrade3 = 0; game.value.biUpgrade4 = 0
}

const resetBn = () => {
  if (game.value.bm < 40) return
  game.value.bn = D(game.value.bn.add(getBnResetReward()))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0); game.value.bz = D(0); game.value.ba = D(0)
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
  game.value.baUpgrade1 = 0; game.value.baUpgrade2 = 0; game.value.baUpgrade3 = 0; game.value.baUpgrade4 = 0
  game.value.bc = D(0); game.value.bd = D(0); game.value.bf = 1; game.value.bg = D(0); game.value.bh = D(0); game.value.bi = D(0)
  game.value.bcUpgrade1 = 0; game.value.bcUpgrade2 = 0; game.value.bcUpgrade3 = 0; game.value.bcUpgrade4 = 0
  game.value.bgUpgrade1 = 0; game.value.bgUpgrade2 = 0; game.value.bgUpgrade3 = 0; game.value.bgUpgrade4 = 0
  game.value.bhUpgrade1 = 0; game.value.bhUpgrade2 = 0; game.value.bhUpgrade3 = 0; game.value.bhUpgrade4 = 0
  game.value.biUpgrade1 = 0; game.value.biUpgrade2 = 0; game.value.biUpgrade3 = 0; game.value.biUpgrade4 = 0
  game.value.bj = D(0); game.value.bk = D(0); game.value.bm = 1
  game.value.bjUpgrade1 = 0; game.value.bjUpgrade2 = 0; game.value.bjUpgrade3 = 0; game.value.bjUpgrade4 = 0
}

const resetBo = () => {
  if (game.value.bm < 120) return
  game.value.bo = D(game.value.bo.add(getBoResetReward()))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0); game.value.bz = D(0); game.value.ba = D(0)
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
  game.value.baUpgrade1 = 0; game.value.baUpgrade2 = 0; game.value.baUpgrade3 = 0; game.value.baUpgrade4 = 0
  game.value.bc = D(0); game.value.bd = D(0); game.value.bf = 1; game.value.bg = D(0); game.value.bh = D(0); game.value.bi = D(0)
  game.value.bcUpgrade1 = 0; game.value.bcUpgrade2 = 0; game.value.bcUpgrade3 = 0; game.value.bcUpgrade4 = 0
  game.value.bgUpgrade1 = 0; game.value.bgUpgrade2 = 0; game.value.bgUpgrade3 = 0; game.value.bgUpgrade4 = 0
  game.value.bhUpgrade1 = 0; game.value.bhUpgrade2 = 0; game.value.bhUpgrade3 = 0; game.value.bhUpgrade4 = 0
  game.value.biUpgrade1 = 0; game.value.biUpgrade2 = 0; game.value.biUpgrade3 = 0; game.value.biUpgrade4 = 0
  game.value.bj = D(0); game.value.bk = D(0); game.value.bm = 1; game.value.bn = D(0)
  game.value.bjUpgrade1 = 0; game.value.bjUpgrade2 = 0; game.value.bjUpgrade3 = 0; game.value.bjUpgrade4 = 0
  game.value.bnUpgrade1 = 0; game.value.bnUpgrade2 = 0; game.value.bnUpgrade3 = 0; game.value.bnUpgrade4 = 0
}

const resetBp = () => {
  if (game.value.bm < 240) return
  game.value.bp = D(game.value.bp.add(getBpResetReward()))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0); game.value.bz = D(0); game.value.ba = D(0)
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
  game.value.baUpgrade1 = 0; game.value.baUpgrade2 = 0; game.value.baUpgrade3 = 0; game.value.baUpgrade4 = 0
  game.value.bc = D(0); game.value.bd = D(0); game.value.bf = 1; game.value.bg = D(0); game.value.bh = D(0); game.value.bi = D(0)
  game.value.bcUpgrade1 = 0; game.value.bcUpgrade2 = 0; game.value.bcUpgrade3 = 0; game.value.bcUpgrade4 = 0
  game.value.bgUpgrade1 = 0; game.value.bgUpgrade2 = 0; game.value.bgUpgrade3 = 0; game.value.bgUpgrade4 = 0
  game.value.bhUpgrade1 = 0; game.value.bhUpgrade2 = 0; game.value.bhUpgrade3 = 0; game.value.bhUpgrade4 = 0
  game.value.biUpgrade1 = 0; game.value.biUpgrade2 = 0; game.value.biUpgrade3 = 0; game.value.biUpgrade4 = 0
  game.value.bj = D(0); game.value.bk = D(0); game.value.bm = 1; game.value.bn = D(0); game.value.bo = D(0)
  game.value.bjUpgrade1 = 0; game.value.bjUpgrade2 = 0; game.value.bjUpgrade3 = 0; game.value.bjUpgrade4 = 0
  game.value.bnUpgrade1 = 0; game.value.bnUpgrade2 = 0; game.value.bnUpgrade3 = 0; game.value.bnUpgrade4 = 0
  game.value.boUpgrade1 = 0; game.value.boUpgrade2 = 0; game.value.boUpgrade3 = 0; game.value.boUpgrade4 = 0
}

const autoUpgradeBm = () => {
  if (game.value.gameEnded) return
  let cost = getBmUpgradeCost(game.value.bm)
  while (game.value.bk.gte(cost) && cost.gt(0)) {
    game.value.bk = D(game.value.bk.sub(cost))
    game.value.bm++
    cost = getBmUpgradeCost(game.value.bm)
  }
}

const upgradeBj = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bjUpgrade1, 90, 1)
      if (game.value.bj.gte(cost)) { game.value.bj = D(game.value.bj.sub(cost)); game.value.bjUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bjUpgrade2, 900, 2)
      if (game.value.bj.gte(cost)) { game.value.bj = D(game.value.bj.sub(cost)); game.value.bjUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bjUpgrade3, 9000, 3)
      if (game.value.bj.gte(cost)) { game.value.bj = D(game.value.bj.sub(cost)); game.value.bjUpgrade3++ }
      break
    case 4:
      if (game.value.bjUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bjUpgrade4, 90000, 4)
        if (game.value.bj.gte(cost)) { game.value.bj = D(game.value.bj.sub(cost)); game.value.bjUpgrade4++ }
      }
      break
  }
}

const upgradeBn = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bnUpgrade1, 90, 1)
      if (game.value.bn.gte(cost)) { game.value.bn = D(game.value.bn.sub(cost)); game.value.bnUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bnUpgrade2, 900, 2)
      if (game.value.bn.gte(cost)) { game.value.bn = D(game.value.bn.sub(cost)); game.value.bnUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bnUpgrade3, 9000, 3)
      if (game.value.bn.gte(cost)) { game.value.bn = D(game.value.bn.sub(cost)); game.value.bnUpgrade3++ }
      break
    case 4:
      if (game.value.bnUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bnUpgrade4, 90000, 4)
        if (game.value.bn.gte(cost)) { game.value.bn = D(game.value.bn.sub(cost)); game.value.bnUpgrade4++ }
      }
      break
  }
}

const upgradeBo = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.boUpgrade1, 90, 1)
      if (game.value.bo.gte(cost)) { game.value.bo = D(game.value.bo.sub(cost)); game.value.boUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.boUpgrade2, 900, 2)
      if (game.value.bo.gte(cost)) { game.value.bo = D(game.value.bo.sub(cost)); game.value.boUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.boUpgrade3, 9000, 3)
      if (game.value.bo.gte(cost)) { game.value.bo = D(game.value.bo.sub(cost)); game.value.boUpgrade3++ }
      break
    case 4:
      if (game.value.boUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.boUpgrade4, 90000, 4)
        if (game.value.bo.gte(cost)) { game.value.bo = D(game.value.bo.sub(cost)); game.value.boUpgrade4++ }
      }
      break
  }
}

const upgradeBp = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bpUpgrade1, 90, 1)
      if (game.value.bp.gte(cost)) { game.value.bp = D(game.value.bp.sub(cost)); game.value.bpUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bpUpgrade2, 900, 2)
      if (game.value.bp.gte(cost)) { game.value.bp = D(game.value.bp.sub(cost)); game.value.bpUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bpUpgrade3, 9000, 3)
      if (game.value.bp.gte(cost)) { game.value.bp = D(game.value.bp.sub(cost)); game.value.bpUpgrade3++ }
      break
    case 4:
      if (game.value.bpUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bpUpgrade4, 90000, 4)
        if (game.value.bp.gte(cost)) { game.value.bp = D(game.value.bp.sub(cost)); game.value.bpUpgrade4++ }
      }
      break
  }
}

// 拤谪层级自动化
const upgradeBjAuto = (autoIndex: number) => {
  if (game.value.gameEnded) return
  const autoKey = `bjAuto${autoIndex}Unlocked` as keyof GameState
  const isUnlocked = game.value[autoKey] as unknown as boolean
  const unlockCosts = [1e17, 1e17, 1e17, 1e17, 1e11, 1e11, 1e11, 1e11]
  const unlockResources = [game.value.bj, game.value.bn, game.value.bo, game.value.bp, game.value.bj, game.value.bn, game.value.bo, game.value.bp]
  if (!isUnlocked) {
    if (unlockResources[autoIndex - 1].gte(unlockCosts[autoIndex - 1])) {
      ;(game.value[autoKey] as unknown as boolean) = true
    }
  }
}

const autoGetBjResetResources = () => {
  if (game.value.bjAuto1Unlocked) {
    game.value.bj = D(game.value.bj.add(getBjClickMultiplier().div(10)))
    game.value.bk = D(game.value.bk.add(getBkClickMultiplier().div(10)))
  }
  if (game.value.bjAuto2Unlocked) {
    game.value.bn = D(game.value.bn.add(getBnResetReward().div(10)))
  }
  if (game.value.bjAuto3Unlocked) {
    game.value.bo = D(game.value.bo.add(getBoResetReward().div(10)))
  }
  if (game.value.bjAuto4Unlocked) {
    game.value.bp = D(game.value.bp.add(getBpResetReward().div(10)))
  }
}

const autoBuyBjUpgrades = () => {
  if (game.value.bjAuto5Unlocked) {
    const upgrades = [
      { level: game.value.bjUpgrade1, base: 90 },
      { level: game.value.bjUpgrade2, base: 900 },
      { level: game.value.bjUpgrade3, base: 9000 },
      { level: game.value.bjUpgrade4, base: 90000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bj, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.bjUpgrade1 += maxBuy; break
          case 1: game.value.bjUpgrade2 += maxBuy; break
          case 2: game.value.bjUpgrade3 += maxBuy; break
          case 3: game.value.bjUpgrade4 = Math.min(Math.floor(game.value.bj.div(9000).add(1).log10()), 9); break
        }
      }
    })
  }
  if (game.value.bjAuto6Unlocked) {
    const upgrades = [
      { level: game.value.bnUpgrade1, base: 90 },
      { level: game.value.bnUpgrade2, base: 900 },
      { level: game.value.bnUpgrade3, base: 9000 },
      { level: game.value.bnUpgrade4, base: 90000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bn, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.bnUpgrade1 += maxBuy; break
          case 1: game.value.bnUpgrade2 += maxBuy; break
          case 2: game.value.bnUpgrade3 += maxBuy; break
          case 3: game.value.bnUpgrade4 = Math.min(Math.floor(game.value.bn.div(9000).add(1).log10()), 9); break
        }
      }
    })
  }
  if (game.value.bjAuto7Unlocked) {
    const upgrades = [
      { level: game.value.boUpgrade1, base: 90 },
      { level: game.value.boUpgrade2, base: 900 },
      { level: game.value.boUpgrade3, base: 9000 },
      { level: game.value.boUpgrade4, base: 90000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bo, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.boUpgrade1 += maxBuy; break
          case 1: game.value.boUpgrade2 += maxBuy; break
          case 2: game.value.boUpgrade3 += maxBuy; break
          case 3: game.value.boUpgrade4 = Math.min(Math.floor(game.value.bo.div(9000).add(1).log10()), 9); break
        }
      }
    })
  }
  if (game.value.bjAuto8Unlocked) {
    const upgrades = [
      { level: game.value.bpUpgrade1, base: 90 },
      { level: game.value.bpUpgrade2, base: 900 },
      { level: game.value.bpUpgrade3, base: 9000 },
      { level: game.value.bpUpgrade4, base: 90000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bp, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.bpUpgrade1 += maxBuy; break
          case 1: game.value.bpUpgrade2 += maxBuy; break
          case 2: game.value.bpUpgrade3 += maxBuy; break
          case 3: game.value.bpUpgrade4 = Math.min(Math.floor(game.value.bp.div(9000).add(1).log10()), 9); break
        }
      }
    })
  }
}

// 拨谮层级计算
const clickBq = (clickMultiplier: number = 1) => {
  if (game.value.bm < 400) return
  const bqMultiplier = getUpgrade1Or2Effect(game.value.bqUpgrade1).mul(getUpgrade1Or2Effect(game.value.btUpgrade1)).mul(getUpgrade1Or2Effect(game.value.buUpgrade1)).mul(getUpgrade1Or2Effect(game.value.bvUpgrade1)).mul(getBwBonus())
  const brMultiplier = getUpgrade1Or2Effect(game.value.bqUpgrade2).mul(getUpgrade1Or2Effect(game.value.btUpgrade2)).mul(getUpgrade1Or2Effect(game.value.buUpgrade2)).mul(getUpgrade1Or2Effect(game.value.bvUpgrade2)).mul(getBwBonus())
  game.value.bq = D(game.value.bq.add(bqMultiplier.mul(clickMultiplier)))
  game.value.br = D(game.value.br.add(brMultiplier.mul(clickMultiplier)))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0); game.value.bz = D(0); game.value.ba = D(0)
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
  game.value.baUpgrade1 = 0; game.value.baUpgrade2 = 0; game.value.baUpgrade3 = 0; game.value.baUpgrade4 = 0
  game.value.bc = D(0); game.value.bd = D(0); game.value.bf = 1; game.value.bg = D(0); game.value.bh = D(0); game.value.bi = D(0)
  game.value.bcUpgrade1 = 0; game.value.bcUpgrade2 = 0; game.value.bcUpgrade3 = 0; game.value.bcUpgrade4 = 0
  game.value.bgUpgrade1 = 0; game.value.bgUpgrade2 = 0; game.value.bgUpgrade3 = 0; game.value.bgUpgrade4 = 0
  game.value.bhUpgrade1 = 0; game.value.bhUpgrade2 = 0; game.value.bhUpgrade3 = 0; game.value.bhUpgrade4 = 0
  game.value.biUpgrade1 = 0; game.value.biUpgrade2 = 0; game.value.biUpgrade3 = 0; game.value.biUpgrade4 = 0
  game.value.bj = D(0); game.value.bk = D(0); game.value.bm = 1; game.value.bn = D(0); game.value.bo = D(0); game.value.bp = D(0)
  game.value.bjUpgrade1 = 0; game.value.bjUpgrade2 = 0; game.value.bjUpgrade3 = 0; game.value.bjUpgrade4 = 0
  game.value.bnUpgrade1 = 0; game.value.bnUpgrade2 = 0; game.value.bnUpgrade3 = 0; game.value.bnUpgrade4 = 0
  game.value.boUpgrade1 = 0; game.value.boUpgrade2 = 0; game.value.boUpgrade3 = 0; game.value.boUpgrade4 = 0
  game.value.bpUpgrade1 = 0; game.value.bpUpgrade2 = 0; game.value.bpUpgrade3 = 0; game.value.bpUpgrade4 = 0
}

const resetBt = () => {
  if (game.value.bs < 40) return
  game.value.bt = D(game.value.bt.add(getBtResetReward()))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0); game.value.bz = D(0); game.value.ba = D(0)
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
  game.value.baUpgrade1 = 0; game.value.baUpgrade2 = 0; game.value.baUpgrade3 = 0; game.value.baUpgrade4 = 0
  game.value.bc = D(0); game.value.bd = D(0); game.value.bf = 1; game.value.bg = D(0); game.value.bh = D(0); game.value.bi = D(0)
  game.value.bcUpgrade1 = 0; game.value.bcUpgrade2 = 0; game.value.bcUpgrade3 = 0; game.value.bcUpgrade4 = 0
  game.value.bgUpgrade1 = 0; game.value.bgUpgrade2 = 0; game.value.bgUpgrade3 = 0; game.value.bgUpgrade4 = 0
  game.value.bhUpgrade1 = 0; game.value.bhUpgrade2 = 0; game.value.bhUpgrade3 = 0; game.value.bhUpgrade4 = 0
  game.value.biUpgrade1 = 0; game.value.biUpgrade2 = 0; game.value.biUpgrade3 = 0; game.value.biUpgrade4 = 0
  game.value.bj = D(0); game.value.bk = D(0); game.value.bm = 1; game.value.bn = D(0); game.value.bo = D(0); game.value.bp = D(0)
  game.value.bjUpgrade1 = 0; game.value.bjUpgrade2 = 0; game.value.bjUpgrade3 = 0; game.value.bjUpgrade4 = 0
  game.value.bnUpgrade1 = 0; game.value.bnUpgrade2 = 0; game.value.bnUpgrade3 = 0; game.value.bnUpgrade4 = 0
  game.value.boUpgrade1 = 0; game.value.boUpgrade2 = 0; game.value.boUpgrade3 = 0; game.value.boUpgrade4 = 0
  game.value.bpUpgrade1 = 0; game.value.bpUpgrade2 = 0; game.value.bpUpgrade3 = 0; game.value.bpUpgrade4 = 0
  game.value.bq = D(0); game.value.br = D(0); game.value.bs = 1
  game.value.bqUpgrade1 = 0; game.value.bqUpgrade2 = 0; game.value.bqUpgrade3 = 0; game.value.bqUpgrade4 = 0
}

const resetBu = () => {
  if (game.value.bs < 120) return
  game.value.bu = D(game.value.bu.add(getBuResetReward()))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0); game.value.bz = D(0); game.value.ba = D(0)
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
  game.value.baUpgrade1 = 0; game.value.baUpgrade2 = 0; game.value.baUpgrade3 = 0; game.value.baUpgrade4 = 0
  game.value.bc = D(0); game.value.bd = D(0); game.value.bf = 1; game.value.bg = D(0); game.value.bh = D(0); game.value.bi = D(0)
  game.value.bcUpgrade1 = 0; game.value.bcUpgrade2 = 0; game.value.bcUpgrade3 = 0; game.value.bcUpgrade4 = 0
  game.value.bgUpgrade1 = 0; game.value.bgUpgrade2 = 0; game.value.bgUpgrade3 = 0; game.value.bgUpgrade4 = 0
  game.value.bhUpgrade1 = 0; game.value.bhUpgrade2 = 0; game.value.bhUpgrade3 = 0; game.value.bhUpgrade4 = 0
  game.value.biUpgrade1 = 0; game.value.biUpgrade2 = 0; game.value.biUpgrade3 = 0; game.value.biUpgrade4 = 0
  game.value.bj = D(0); game.value.bk = D(0); game.value.bm = 1; game.value.bn = D(0); game.value.bo = D(0); game.value.bp = D(0)
  game.value.bjUpgrade1 = 0; game.value.bjUpgrade2 = 0; game.value.bjUpgrade3 = 0; game.value.bjUpgrade4 = 0
  game.value.bnUpgrade1 = 0; game.value.bnUpgrade2 = 0; game.value.bnUpgrade3 = 0; game.value.bnUpgrade4 = 0
  game.value.boUpgrade1 = 0; game.value.boUpgrade2 = 0; game.value.boUpgrade3 = 0; game.value.boUpgrade4 = 0
  game.value.bpUpgrade1 = 0; game.value.bpUpgrade2 = 0; game.value.bpUpgrade3 = 0; game.value.bpUpgrade4 = 0
  game.value.bq = D(0); game.value.br = D(0); game.value.bs = 1; game.value.bt = D(0)
  game.value.bqUpgrade1 = 0; game.value.bqUpgrade2 = 0; game.value.bqUpgrade3 = 0; game.value.bqUpgrade4 = 0
  game.value.btUpgrade1 = 0; game.value.btUpgrade2 = 0; game.value.btUpgrade3 = 0; game.value.btUpgrade4 = 0
}

const resetBv = () => {
  if (game.value.bs < 240) return
  game.value.bv = D(game.value.bv.add(getBvResetReward()))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0); game.value.bz = D(0); game.value.ba = D(0)
  game.value.bxUpgrade1 = 0; game.value.bxUpgrade2 = 0; game.value.bxUpgrade3 = 0; game.value.bxUpgrade4 = 0
  game.value.byUpgrade1 = 0; game.value.byUpgrade2 = 0; game.value.byUpgrade3 = 0; game.value.byUpgrade4 = 0
  game.value.bzUpgrade1 = 0; game.value.bzUpgrade2 = 0; game.value.bzUpgrade3 = 0; game.value.bzUpgrade4 = 0
  game.value.baUpgrade1 = 0; game.value.baUpgrade2 = 0; game.value.baUpgrade3 = 0; game.value.baUpgrade4 = 0
  game.value.bc = D(0); game.value.bd = D(0); game.value.bf = 1; game.value.bg = D(0); game.value.bh = D(0); game.value.bi = D(0)
  game.value.bcUpgrade1 = 0; game.value.bcUpgrade2 = 0; game.value.bcUpgrade3 = 0; game.value.bcUpgrade4 = 0
  game.value.bgUpgrade1 = 0; game.value.bgUpgrade2 = 0; game.value.bgUpgrade3 = 0; game.value.bgUpgrade4 = 0
  game.value.bhUpgrade1 = 0; game.value.bhUpgrade2 = 0; game.value.bhUpgrade3 = 0; game.value.bhUpgrade4 = 0
  game.value.biUpgrade1 = 0; game.value.biUpgrade2 = 0; game.value.biUpgrade3 = 0; game.value.biUpgrade4 = 0
  game.value.bj = D(0); game.value.bk = D(0); game.value.bm = 1; game.value.bn = D(0); game.value.bo = D(0); game.value.bp = D(0)
  game.value.bjUpgrade1 = 0; game.value.bjUpgrade2 = 0; game.value.bjUpgrade3 = 0; game.value.bjUpgrade4 = 0
  game.value.bnUpgrade1 = 0; game.value.bnUpgrade2 = 0; game.value.bnUpgrade3 = 0; game.value.bnUpgrade4 = 0
  game.value.boUpgrade1 = 0; game.value.boUpgrade2 = 0; game.value.boUpgrade3 = 0; game.value.boUpgrade4 = 0
  game.value.bpUpgrade1 = 0; game.value.bpUpgrade2 = 0; game.value.bpUpgrade3 = 0; game.value.bpUpgrade4 = 0
  game.value.bq = D(0); game.value.br = D(0); game.value.bs = 1; game.value.bt = D(0); game.value.bu = D(0)
  game.value.bqUpgrade1 = 0; game.value.bqUpgrade2 = 0; game.value.bqUpgrade3 = 0; game.value.bqUpgrade4 = 0
  game.value.btUpgrade1 = 0; game.value.btUpgrade2 = 0; game.value.btUpgrade3 = 0; game.value.btUpgrade4 = 0
  game.value.buUpgrade1 = 0; game.value.buUpgrade2 = 0; game.value.buUpgrade3 = 0; game.value.buUpgrade4 = 0
}

const autoUpgradeBs = () => {
  if (game.value.gameEnded) return
  let cost = getBsUpgradeCost(game.value.bs)
  while (game.value.br.gte(cost) && cost.gt(0)) {
    game.value.br = D(game.value.br.sub(cost))
    game.value.bs++
    cost = getBsUpgradeCost(game.value.bs)
  }
}

const upgradeBq = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bqUpgrade1, 270, 1)
      if (game.value.bq.gte(cost)) { game.value.bq = D(game.value.bq.sub(cost)); game.value.bqUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bqUpgrade2, 2700, 2)
      if (game.value.bq.gte(cost)) { game.value.bq = D(game.value.bq.sub(cost)); game.value.bqUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bqUpgrade3, 27000, 3)
      if (game.value.bq.gte(cost)) { game.value.bq = D(game.value.bq.sub(cost)); game.value.bqUpgrade3++ }
      break
    case 4:
      if (game.value.bqUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bqUpgrade4, 270000, 4)
        if (game.value.bq.gte(cost)) { game.value.bq = D(game.value.bq.sub(cost)); game.value.bqUpgrade4++ }
      }
      break
  }
}

const upgradeBt = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.btUpgrade1, 270, 1)
      if (game.value.bt.gte(cost)) { game.value.bt = D(game.value.bt.sub(cost)); game.value.btUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.btUpgrade2, 2700, 2)
      if (game.value.bt.gte(cost)) { game.value.bt = D(game.value.bt.sub(cost)); game.value.btUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.btUpgrade3, 27000, 3)
      if (game.value.bt.gte(cost)) { game.value.bt = D(game.value.bt.sub(cost)); game.value.btUpgrade3++ }
      break
    case 4:
      if (game.value.btUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.btUpgrade4, 270000, 4)
        if (game.value.bt.gte(cost)) { game.value.bt = D(game.value.bt.sub(cost)); game.value.btUpgrade4++ }
      }
      break
  }
}

const upgradeBu = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.buUpgrade1, 270, 1)
      if (game.value.bu.gte(cost)) { game.value.bu = D(game.value.bu.sub(cost)); game.value.buUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.buUpgrade2, 2700, 2)
      if (game.value.bu.gte(cost)) { game.value.bu = D(game.value.bu.sub(cost)); game.value.buUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.buUpgrade3, 27000, 3)
      if (game.value.bu.gte(cost)) { game.value.bu = D(game.value.bu.sub(cost)); game.value.buUpgrade3++ }
      break
    case 4:
      if (game.value.buUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.buUpgrade4, 270000, 4)
        if (game.value.bu.gte(cost)) { game.value.bu = D(game.value.bu.sub(cost)); game.value.buUpgrade4++ }
      }
      break
  }
}

const upgradeBv = (upgrade: number) => {
  if (game.value.gameEnded) return
  let cost = D(0)
  switch (upgrade) {
    case 1:
      cost = getUpgradeCost(game.value.bvUpgrade1, 270, 1)
      if (game.value.bv.gte(cost)) { game.value.bv = D(game.value.bv.sub(cost)); game.value.bvUpgrade1++ }
      break
    case 2:
      cost = getUpgradeCost(game.value.bvUpgrade2, 2700, 2)
      if (game.value.bv.gte(cost)) { game.value.bv = D(game.value.bv.sub(cost)); game.value.bvUpgrade2++ }
      break
    case 3:
      cost = getUpgradeCost(game.value.bvUpgrade3, 27000, 3)
      if (game.value.bv.gte(cost)) { game.value.bv = D(game.value.bv.sub(cost)); game.value.bvUpgrade3++ }
      break
    case 4:
      if (game.value.bvUpgrade4 < 9) {
        cost = getUpgradeCost(game.value.bvUpgrade4, 270000, 4)
        if (game.value.bv.gte(cost)) { game.value.bv = D(game.value.bv.sub(cost)); game.value.bvUpgrade4++ }
      }
      break
  }
}

// 拨谮层级自动化
const upgradeBqAuto = (autoIndex: number) => {
  if (game.value.gameEnded) return
  const autoKey = `bqAuto${autoIndex}Unlocked` as keyof GameState
  const isUnlocked = game.value[autoKey] as unknown as boolean
  const unlockCosts = [1e18, 1e18, 1e18, 1e18, 1e12, 1e12, 1e12, 1e12]
  const unlockResources = [game.value.bq, game.value.bt, game.value.bu, game.value.bv, game.value.bq, game.value.bt, game.value.bu, game.value.bv]
  if (!isUnlocked) {
    if (unlockResources[autoIndex - 1].gte(unlockCosts[autoIndex - 1])) {
      ;(game.value[autoKey] as unknown as boolean) = true
    }
  }
}

const autoGetBqResetResources = () => {
  if (game.value.bqAuto1Unlocked) {
    game.value.bq = D(game.value.bq.add(getBqClickMultiplier().div(10)))
    game.value.br = D(game.value.br.add(getBrClickMultiplier().div(10)))
  }
  if (game.value.bqAuto2Unlocked) {
    game.value.bt = D(game.value.bt.add(getBtResetReward().div(10)))
  }
  if (game.value.bqAuto3Unlocked) {
    game.value.bu = D(game.value.bu.add(getBuResetReward().div(10)))
  }
  if (game.value.bqAuto4Unlocked) {
    game.value.bv = D(game.value.bv.add(getBvResetReward().div(10)))
  }
}

const autoBuyBqUpgrades = () => {
  if (game.value.bqAuto5Unlocked) {
    const upgrades = [
      { level: game.value.bqUpgrade1, base: 270 },
      { level: game.value.bqUpgrade2, base: 2700 },
      { level: game.value.bqUpgrade3, base: 27000 },
      { level: game.value.bqUpgrade4, base: 270000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bq, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.bqUpgrade1 += maxBuy; break
          case 1: game.value.bqUpgrade2 += maxBuy; break
          case 2: game.value.bqUpgrade3 += maxBuy; break
          case 3: game.value.bqUpgrade4 = Math.min(Math.floor(game.value.bq.div(27000).add(1).log10()), 9); break
        }
      }
    })
  }
  if (game.value.bqAuto6Unlocked) {
    const upgrades = [
      { level: game.value.btUpgrade1, base: 270 },
      { level: game.value.btUpgrade2, base: 2700 },
      { level: game.value.btUpgrade3, base: 27000 },
      { level: game.value.btUpgrade4, base: 270000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bt, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.btUpgrade1 += maxBuy; break
          case 1: game.value.btUpgrade2 += maxBuy; break
          case 2: game.value.btUpgrade3 += maxBuy; break
          case 3: game.value.btUpgrade4 = Math.min(Math.floor(game.value.bt.div(27000).add(1).log10()), 9); break
        }
      }
    })
  }
  if (game.value.bqAuto7Unlocked) {
    const upgrades = [
      { level: game.value.buUpgrade1, base: 270 },
      { level: game.value.buUpgrade2, base: 2700 },
      { level: game.value.buUpgrade3, base: 27000 },
      { level: game.value.buUpgrade4, base: 270000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bu, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.buUpgrade1 += maxBuy; break
          case 1: game.value.buUpgrade2 += maxBuy; break
          case 2: game.value.buUpgrade3 += maxBuy; break
          case 3: game.value.buUpgrade4 = Math.min(Math.floor(game.value.bu.div(27000).add(1).log10()), 9); break
        }
      }
    })
  }
  if (game.value.bqAuto8Unlocked) {
    const upgrades = [
      { level: game.value.bvUpgrade1, base: 270 },
      { level: game.value.bvUpgrade2, base: 2700 },
      { level: game.value.bvUpgrade3, base: 27000 },
      { level: game.value.bvUpgrade4, base: 270000 }
    ]
    upgrades.forEach((upgrade, index) => {
      const maxBuy = calculateMaxPurchases(game.value.bv, upgrade.base, UPGRADE_COST_MULTIPLIER_1_3, upgrade.level)
      if (maxBuy > 0) {
        switch (index) {
          case 0: game.value.bvUpgrade1 += maxBuy; break
          case 1: game.value.bvUpgrade2 += maxBuy; break
          case 2: game.value.bvUpgrade3 += maxBuy; break
          case 3: game.value.bvUpgrade4 = Math.min(Math.floor(game.value.bv.div(27000).add(1).log10()), 9); break
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
    autoGetResetResources()
    autoGetBcResetResources()
    autoGetBjResetResources()
    autoGetBqResetResources()
    autoBuyUpgrades()
    autoBuyBcUpgrades()
    autoBuyBjUpgrades()
    autoBuyBqUpgrades()
    autoUpgradeBl()
    autoUpgradeBf()
    autoUpgradeBm()
    autoUpgradeBs()
    if (game.value.bw.gte(114514)) { game.value.bw = D(114514); game.value.gameEnded = true }
  }, 100)
  saveInterval = window.setInterval(() => saveGame(), 10000)
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
const deserializeGameState = (json: string): GameState => {
  const parsed = JSON.parse(json)
  const state = { ...defaultSave }
  for (const key of Object.keys(parsed) as (keyof GameState)[]) {
    if (DECIMAL_FIELDS.has(key)) {
      (state as any)[key] = D(parsed[key])
    } else if (key in state) {
      (state as any)[key] = parsed[key]
    }
  }
  return state
}

const loadGame = () => {
  const saved = localStorage.getItem('baixie_save')
  if (saved) {
    try { game.value = deserializeGameState(saved) } catch (e) { console.error('Failed to load save', e) }
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
      game.value = deserializeGameState(decompressed)
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
  if (!game.value.isBwUnlocked) return
  if (game.value.bs < 400) return
  game.value.bw = D(game.value.bw.add(getBwResetReward()))
  game.value.bx = D(0); game.value.be = D(0); game.value.bl = 1; game.value.by = D(0); game.value.bz = D(0); game.value.ba = D(0)
  game.value.bc = D(0); game.value.bd = D(0); game.value.bf = 1; game.value.bg = D(0); game.value.bh = D(0); game.value.bi = D(0)
  game.value.bj = D(0); game.value.bk = D(0); game.value.bm = 1; game.value.bn = D(0); game.value.bo = D(0); game.value.bp = D(0)
  game.value.bq = D(0); game.value.br = D(0); game.value.bs = 1; game.value.bt = D(0); game.value.bu = D(0); game.value.bv = D(0)
  Object.keys(game.value).forEach(key => { if (key.includes('Upgrade')) (game.value[key as keyof GameState] as unknown as number) = 0 })
  game.value.bxAuto1Unlocked = false; game.value.bxAuto2Unlocked = false; game.value.bxAuto3Unlocked = false; game.value.bxAuto4Unlocked = false
  game.value.bxAuto5Unlocked = false; game.value.bxAuto6Unlocked = false; game.value.bxAuto7Unlocked = false; game.value.bxAuto8Unlocked = false
  game.value.bcAuto1Unlocked = false; game.value.bcAuto2Unlocked = false; game.value.bcAuto3Unlocked = false; game.value.bcAuto4Unlocked = false
  game.value.bcAuto5Unlocked = false; game.value.bcAuto6Unlocked = false; game.value.bcAuto7Unlocked = false; game.value.bcAuto8Unlocked = false
  game.value.bjAuto1Unlocked = false; game.value.bjAuto2Unlocked = false; game.value.bjAuto3Unlocked = false; game.value.bjAuto4Unlocked = false
  game.value.bjAuto5Unlocked = false; game.value.bjAuto6Unlocked = false; game.value.bjAuto7Unlocked = false; game.value.bjAuto8Unlocked = false
  game.value.bqAuto1Unlocked = false; game.value.bqAuto2Unlocked = false; game.value.bqAuto3Unlocked = false; game.value.bqAuto4Unlocked = false
  game.value.bqAuto5Unlocked = false; game.value.bqAuto6Unlocked = false; game.value.bqAuto7Unlocked = false; game.value.bqAuto8Unlocked = false
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
watch(() => game.value.bx, () => {
  document.title = `你有${ formatNumber(game.value.bx) }拜谢`
}, { immediate: true })
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
          <img src="/baixie.png" alt="" class="ending-img" v-for="i in 10" :key="i" />
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
          v-if="game.isBcUnlocked"
          :class="{ active: activeTab === 'juqian' }" 
          @click="activeTab = 'juqian'" 
          class="tab-btn">
          拠谦层级
        </button>
        <button 
          v-if="game.isBjUnlocked"
          :class="{ active: activeTab === 'jiadi' }" 
          @click="activeTab = 'jiadi'" 
          class="tab-btn">
          拤谪层级
        </button>
        <button 
          v-if="game.isBqUnlocked"
          :class="{ active: activeTab === 'boxie' }" 
          @click="activeTab = 'boxie'" 
          class="tab-btn">
          拨谮层级
        </button>
        <button 
          v-if="game.isBwUnlocked"
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
          <div class="progress-fill" :style="{ width: Math.min(game.be.div(getBlUpgradeCost(game.bl)).toNumber() * 100, 100) + '%' }">
            <span class="progress-text" style="position: absolute; left: 50%; transform: translateX(-50%);">{{ formatNumber(game.be) }} / {{ formatNumber(getBlUpgradeCost(game.bl)) }}</span>
          </div>
        </div>
        <span class="bonus">拜谢加成：×{{ formatNumber(getLevelBonus(game.bl)) }}</span>
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
          <button @click="upgradeBx(1)" :disabled="game.bx.lt(getUpgradeCost(game.bxUpgrade1, 10, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拜谢获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bxUpgrade1)) }} (Lv.{{ game.bxUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bxUpgrade1, 10, 1)) }} 拜谢
          </button>
          <button @click="upgradeBx(2)" :disabled="game.bx.lt(getUpgradeCost(game.bxUpgrade2, 100, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拜谢经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bxUpgrade2)) }} (Lv.{{ game.bxUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bxUpgrade2, 100, 2)) }} 拜谢
          </button>
          <button @click="upgradeBx(3)" :disabled="game.bx.lt(getUpgradeCost(game.bxUpgrade3, 1000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拝谣获取 ×{{ formatNumber(getUpgrade3Effect(game.bxUpgrade3)) }} (Lv.{{ game.bxUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bxUpgrade3, 1000, 3)) }} 拜谢
          </button>
          <button @click="upgradeBx(4)" :disabled="game.bx.lt(getUpgradeCost(game.bxUpgrade4, 10000, 4))|| game.bxUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bxUpgrade4)) }} (Lv.{{ game.bxUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bxUpgrade4, 10000, 4)) }} 拜谢
          </button>
        </div>
        <div class="upgrades" v-if="game.bl >= 40 || game.by.gt(0)">
          <h3>拝谣升级</h3>
          <button @click="upgradeBy(1)" :disabled="game.by.lt(getUpgradeCost(game.byUpgrade1, 10, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拜谢获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.byUpgrade1)) }} (Lv.{{ game.byUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.byUpgrade1, 10, 1)) }} 拝谣
          </button>
          <button @click="upgradeBy(2)" :disabled="game.by.lt(getUpgradeCost(game.byUpgrade2, 100, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拜谢经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.byUpgrade2)) }} (Lv.{{ game.byUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.byUpgrade2, 100, 2)) }} 拝谣
          </button>
          <button @click="upgradeBy(3)" :disabled="game.by.lt(getUpgradeCost(game.byUpgrade3, 1000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拞谤获取 ×{{ formatNumber(getUpgrade3Effect(game.byUpgrade3)) }} (Lv.{{ game.byUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.byUpgrade3, 1000, 3)) }} 拝谣
          </button>
          <button @click="upgradeBy(4)" :disabled="game.by.lt(getUpgradeCost(game.byUpgrade4, 10000, 4))|| game.byUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.byUpgrade4)) }} (Lv.{{ game.byUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.byUpgrade4, 10000, 4)) }} 拝谣
          </button>
        </div>
        <div class="upgrades" v-if="game.bl >= 120 || game.bz.gt(0)">
          <h3>拞谤升级</h3>
          <button @click="upgradeBz(1)" :disabled="game.bz.lt(getUpgradeCost(game.bzUpgrade1, 10, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拜谢获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bzUpgrade1)) }} (Lv.{{ game.bzUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bzUpgrade1, 10, 1)) }} 拞谤
          </button>
          <button @click="upgradeBz(2)" :disabled="game.bz.lt(getUpgradeCost(game.bzUpgrade2, 100, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拜谢经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bzUpgrade2)) }} (Lv.{{ game.bzUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bzUpgrade2, 100, 2)) }} 拞谤
          </button>
          <button @click="upgradeBz(3)" :disabled="game.bz.lt(getUpgradeCost(game.bzUpgrade3, 1000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拟谥获取 ×{{ formatNumber(getUpgrade3Effect(game.bzUpgrade3)) }} (Lv.{{ game.bzUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bzUpgrade3, 1000, 3)) }} 拞谤
          </button>
          <button @click="upgradeBz(4)" :disabled="game.bz.lt(getUpgradeCost(game.bzUpgrade4, 10000, 4))|| game.bzUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bzUpgrade4)) }} (Lv.{{ game.bzUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bzUpgrade4, 10000, 4)) }} 拞谤
          </button>
        </div>
        <div class="upgrades" v-if="game.bl >= 240 || game.ba.gt(0)">
          <h3>拟谥升级</h3>
          <button @click="upgradeBa(1)" :disabled="game.ba.lt(getUpgradeCost(game.baUpgrade1, 10, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拜谢获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.baUpgrade1)) }} (Lv.{{ game.baUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.baUpgrade1, 10, 1)) }} 拟谥
          </button>
          <button @click="upgradeBa(2)" :disabled="game.ba.lt(getUpgradeCost(game.baUpgrade2, 100, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拜谢经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.baUpgrade2)) }} (Lv.{{ game.baUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.baUpgrade2, 100, 2)) }} 拟谥
          </button>
          <button @click="upgradeBa(3)" :disabled="game.ba.lt(getUpgradeCost(game.baUpgrade3, 1000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拠谦获取 ×{{ formatNumber(getUpgrade3Effect(game.baUpgrade3)) }} (Lv.{{ game.baUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.baUpgrade3, 1000, 3)) }} 拟谥
          </button>
          <button @click="upgradeBa(4)" :disabled="game.ba.lt(getUpgradeCost(game.baUpgrade4, 10000, 4))|| game.baUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.baUpgrade4)) }} (Lv.{{ game.baUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.baUpgrade4, 10000, 4)) }} 拟谥
          </button>
        </div>
        <div class="section">
        <h2>拜谢自动化</h2>
        <div class="auto-upgrades">
          <div v-for="i in 8" :key="i" class="auto-upgrade">
            <button @click="upgradeBxAuto(i)" :class="{ locked: !autoUnlocked(i) }" class="auto-btn">
              <img src="/baixie.png" alt="" class="btn-icon" /><div class="auto-title">{{ autoUnlocked(i) ? `拜谢自动化${i} 已解锁` : `拜谢自动化${i}` }}</div>
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
        <h2>拠谦层级</h2>
        <div class="resources">
          <div class="resource">拠谦：<span class="counter">{{ formatNumber(game.bc) }}</span></div>
          <div class="resource">经验：<span class="counter">{{ formatNumber(game.bd) }}</span></div>
          <div class="resource">等级：<span class="counter">{{ game.bf }}</span></div>
          <div class="resource">拡谧：<span class="counter">{{ formatNumber(game.bg) }}</span></div>
          <div class="resource">拢谨：<span class="counter">{{ formatNumber(game.bh) }}</span></div>
          <div class="resource">拣谩：<span class="counter">{{ formatNumber(game.bi) }}</span></div>
        </div>
        <div class="progress-bar" v-if="game.isBcUnlocked">
          <div class="progress-fill" :style="{ width: Math.min(game.bd.div(getBfUpgradeCost(game.bf)).toNumber() * 100, 100) + '%' }">
            <span class="progress-text" style="position: absolute; left: 50%; transform: translateX(-50%);">{{ formatNumber(game.bd) }} / {{ formatNumber(getBlUpgradeCost(game.bf)) }}</span>
          </div>
        </div>
        <span class="bonus" v-if="game.isBcUnlocked">上一层资源加成：×{{ formatNumber(getLevelBonus(game.bf)) }}</span>
          <button @click="clickBc(1)" class="reset-btn" :disabled="game.bl < 400">
            <img src="/baixie.png" alt="" class="btn-icon" />拠谦一次(需要 400 拜谢等级)：重置之前层级除自动化外的所有内容，获得 {{ formatNumber(getBcClickMultiplier()) }} 拠谦，{{ formatNumber(getBdClickMultiplier()) }} 经验
          </button>
          <button @click="resetBg" class="reset-btn" :disabled="game.bf < 40">
            <img src="/baixie.png" alt="" class="btn-icon" />拡谧重置(需要 40 拠谦等级)：重置拠谦重置的东西、拠谦和拠谦升级，获得 {{ formatNumber(getBgResetReward()) }} 拡谧
          </button>
          <button @click="resetBh" class="reset-btn" :disabled="game.bf < 120">
            <img src="/baixie.png" alt="" class="btn-icon" />拢谨重置(需要 120 拠谦等级)：重置拡谧重置的东西、拡谧和拡谧升级，获得 {{ formatNumber(getBhResetReward()) }} 拢谨
          </button>
          <button @click="resetBi" class="reset-btn" :disabled="game.bf < 240">
            <img src="/baixie.png" alt="" class="btn-icon" />拣谩重置(需要 240 拠谦等级)：重置拢谨重置的东西、拢谨和拢谨升级，获得 {{ formatNumber(getBiResetReward()) }} 拣谩
          </button>
        <div class="upgrades" v-if="game.isBcUnlocked">
          <h3>拠谦升级</h3>
          <button @click="upgradeBc(1)" :disabled="game.bc.lt(getUpgradeCost(game.bcUpgrade1, 30, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拠谦获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bcUpgrade1)) }} (Lv.{{ game.bcUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bcUpgrade1, 30, 1)) }} 拠谦
          </button>
          <button @click="upgradeBc(2)" :disabled="game.bc.lt(getUpgradeCost(game.bcUpgrade2, 300, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拠谦经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bcUpgrade2)) }} (Lv.{{ game.bcUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bcUpgrade2, 300, 2)) }} 拠谦
          </button>
          <button @click="upgradeBc(3)" :disabled="game.bc.lt(getUpgradeCost(game.bcUpgrade3, 3000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拡谧获取 ×{{ formatNumber(getUpgrade3Effect(game.bcUpgrade3)) }} (Lv.{{ game.bcUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bcUpgrade3, 3000, 3)) }} 拠谦
          </button>
          <button @click="upgradeBc(4)" :disabled="game.bc.lt(getUpgradeCost(game.bcUpgrade4, 30000, 4))|| game.bcUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bcUpgrade4)) }} (Lv.{{ game.bcUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bcUpgrade4, 30000, 4)) }} 拠谦
          </button>
        </div>
        <div class="upgrades" v-if="game.bf >= 40 || game.bg.gt(0)">
          <h3>拡谧升级</h3>
          <button @click="upgradeBg(1)" :disabled="game.bg.lt(getUpgradeCost(game.bgUpgrade1, 30, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拠谦获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bgUpgrade1)) }} (Lv.{{ game.bgUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bgUpgrade1, 30, 1)) }} 拡谧
          </button>
          <button @click="upgradeBg(2)" :disabled="game.bg.lt(getUpgradeCost(game.bgUpgrade2, 300, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拠谦经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bgUpgrade2)) }} (Lv.{{ game.bgUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bgUpgrade2, 300, 2)) }} 拡谧
          </button>
          <button @click="upgradeBg(3)" :disabled="game.bg.lt(getUpgradeCost(game.bgUpgrade3, 3000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拢谨获取 ×{{ formatNumber(getUpgrade3Effect(game.bgUpgrade3)) }} (Lv.{{ game.bgUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bgUpgrade3, 3000, 3)) }} 拡谧
          </button>
          <button @click="upgradeBg(4)" :disabled="game.bg.lt(getUpgradeCost(game.bgUpgrade4, 30000, 4))|| game.bgUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bgUpgrade4)) }} (Lv.{{ game.bgUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bgUpgrade4, 30000, 4)) }} 拡谧
          </button>
        </div>
        <div class="upgrades" v-if="game.bf >= 120 || game.bh.gt(0)">
          <h3>拢谨升级</h3>
          <button @click="upgradeBh(1)" :disabled="game.bh.lt(getUpgradeCost(game.bhUpgrade1, 30, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拠谦获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bhUpgrade1)) }} (Lv.{{ game.bhUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bhUpgrade1, 30, 1)) }} 拢谨
          </button>
          <button @click="upgradeBh(2)" :disabled="game.bh.lt(getUpgradeCost(game.bhUpgrade2, 300, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拠谦经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bhUpgrade2)) }} (Lv.{{ game.bhUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bhUpgrade2, 300, 2)) }} 拢谨
          </button>
          <button @click="upgradeBh(3)" :disabled="game.bh.lt(getUpgradeCost(game.bhUpgrade3, 3000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拣谩获取 ×{{ formatNumber(getUpgrade3Effect(game.bhUpgrade3)) }} (Lv.{{ game.bhUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bhUpgrade3, 3000, 3)) }} 拢谨
          </button>
          <button @click="upgradeBh(4)" :disabled="game.bh.lt(getUpgradeCost(game.bhUpgrade4, 30000, 4))|| game.bhUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bhUpgrade4)) }} (Lv.{{ game.bhUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bhUpgrade4, 30000, 4)) }} 拢谨
          </button>
        </div>
        <div class="upgrades" v-if="game.bf >= 240 || game.bi.gt(0)">
          <h3>拣谩升级</h3>
          <button @click="upgradeBi(1)" :disabled="game.bi.lt(getUpgradeCost(game.biUpgrade1, 30, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拠谦获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.biUpgrade1)) }} (Lv.{{ game.biUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.biUpgrade1, 30, 1)) }} 拣谩
          </button>
          <button @click="upgradeBi(2)" :disabled="game.bi.lt(getUpgradeCost(game.biUpgrade2, 300, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拠谦经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.biUpgrade2)) }} (Lv.{{ game.biUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.biUpgrade2, 300, 2)) }} 拣谩
          </button>
          <button @click="upgradeBi(3)" :disabled="game.bi.lt(getUpgradeCost(game.biUpgrade3, 3000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拣谩获取 ×{{ formatNumber(getUpgrade3Effect(game.biUpgrade3)) }} (Lv.{{ game.biUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.biUpgrade3, 3000, 3)) }} 拣谩
          </button>
          <button @click="upgradeBi(4)" :disabled="game.bi.lt(getUpgradeCost(game.biUpgrade4, 30000, 4))|| game.biUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.biUpgrade4)) }} (Lv.{{ game.biUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.biUpgrade4, 30000, 4)) }} 拣谩
          </button>
        </div>
        <div class="section">
          <h2>拠谦自动化</h2>
          <div class="auto-upgrades">
            <div v-for="i in 8" :key="i" class="auto-upgrade">
              <button @click="upgradeBcAuto(i)" :class="{ locked: !(game as any)[`bcAuto${i}Unlocked`] }" class="auto-btn">
                <img src="/baixie.png" alt="" class="btn-icon" />
                <div class="auto-title">{{ (game as any)[`bcAuto${i}Unlocked`] ? `拠谦自动化${i} 已解锁` : `拠谦自动化${i}` }}</div>
                <div class="auto-desc">
                  <span v-if="i===1">每秒自动获得10%重置时获得的拠谦及其经验</span>
                  <span v-else-if="i===2">每秒自动获得10%重置时获得的拡谧</span>
                  <span v-else-if="i===3">每秒自动获得10%重置时获得的拢谨</span>
                  <span v-else-if="i===4">每秒自动获得10%重置时获得的拣谩</span>
                  <span v-else-if="i===5">自动购买拠谦升级且不消耗拠谦</span>
                  <span v-else-if="i===6">自动购买拡谧升级且不消耗拡谧</span>
                  <span v-else-if="i===7">自动购买拢谨升级且不消耗拢谨</span>
                  <span v-else>自动购买拣谩升级且不消耗拣谩</span>
                </div>
                <div class="auto-cost">
                  花费：{{ formatNumber([1e16,1e16,1e16,1e16,1e10,1e10,1e10,1e10][i-1]) }} {{ ['拠谦','拡谧','拢谨','拣漫','拠谦','拡谧','拢谨','拣谩'][i-1] }}
                </div>
              </button>
            </div>
          </div>
        </div>
      </div>
      
      <!-- 拤谪层级标签页 -->
      <div v-show="activeTab === 'jiadi'" class="tab-content">
        <h2>拤谪层级</h2>
        <div class="resources">
          <div class="resource">拤谪：<span class="counter">{{ formatNumber(game.bj) }}</span></div>
          <div class="resource">经验：<span class="counter">{{ formatNumber(game.bk) }}</span></div>
          <div class="resource">等级：<span class="counter">{{ game.bm }}</span></div>
          <div class="resource">拥谫：<span class="counter">{{ formatNumber(game.bn) }}</span></div>
          <div class="resource">拦谬：<span class="counter">{{ formatNumber(game.bo) }}</span></div>
          <div class="resource">拧谭：<span class="counter">{{ formatNumber(game.bp) }}</span></div>
        </div>
        <div class="progress-bar" v-if="game.isBjUnlocked">
          <div class="progress-fill" :style="{ width: Math.min(game.bk.div(getBmUpgradeCost(game.bm)).toNumber() * 100, 100) + '%' }">
            <span class="progress-text" style="position: absolute; left: 50%; transform: translateX(-50%);">{{ formatNumber(game.bk) }} / {{ formatNumber(getBmUpgradeCost(game.bm)) }}</span>
          </div>
        </div>
        <span class="bonus" v-if="game.isBjUnlocked">上一层资源加成：×{{ formatNumber(getLevelBonus(game.bm)) }}</span>
          <button @click="clickBj(1)" class="reset-btn" :disabled="game.bf < 400">
            <img src="/baixie.png" alt="" class="btn-icon" />拤谪一次(需要 400 拠谦等级)：重置之前层级除自动化外的所有内容，获得 {{ formatNumber(getBjClickMultiplier()) }} 拤谪，{{ formatNumber(getBkClickMultiplier()) }} 经验
          </button>
          <button @click="resetBn" class="reset-btn" :disabled="game.bm < 40">
            <img src="/baixie.png" alt="" class="btn-icon" />拥谫重置(需要 40 拤谪等级)：重置拤谪重置的东西、拤谪和拤谪升级，获得 {{ formatNumber(getBnResetReward()) }} 拥谫
          </button>
          <button @click="resetBo" class="reset-btn" :disabled="game.bm < 120">
            <img src="/baixie.png" alt="" class="btn-icon" />拦谬重置(需要 120 拤谪等级)：重置拥谫重置的东西、拥谫和拥谫升级，获得 {{ formatNumber(getBoResetReward()) }} 拦谬
          </button>
          <button @click="resetBp" class="reset-btn" :disabled="game.bm < 240">
            <img src="/baixie.png" alt="" class="btn-icon" />拧谭重置(需要 240 拤谪等级)：重置拦谬重置的东西、拦谬和拦谬升级，获得 {{ formatNumber(getBpResetReward()) }} 拧谭
          </button>
        <div class="upgrades" v-if="game.isBjUnlocked">
          <h3>拤谪升级</h3>
          <button @click="upgradeBj(1)" :disabled="game.bj.lt(getUpgradeCost(game.bjUpgrade1, 90, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拤谪获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bjUpgrade1)) }} (Lv.{{ game.bjUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bjUpgrade1, 90, 1)) }} 拤谪
          </button>
          <button @click="upgradeBj(2)" :disabled="game.bj.lt(getUpgradeCost(game.bjUpgrade2, 900, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拤谪经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bjUpgrade2)) }} (Lv.{{ game.bjUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bjUpgrade2, 900, 2)) }} 拤谪
          </button>
          <button @click="upgradeBj(3)" :disabled="game.bj.lt(getUpgradeCost(game.bjUpgrade3, 9000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拥谫获取 ×{{ formatNumber(getUpgrade3Effect(game.bjUpgrade3)) }} (Lv.{{ game.bjUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bjUpgrade3, 9000, 3)) }} 拤谪
          </button>
          <button @click="upgradeBj(4)" :disabled="game.bj.lt(getUpgradeCost(game.bjUpgrade4, 90000, 4))|| game.bjUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bjUpgrade4)) }} (Lv.{{ game.bjUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bjUpgrade4, 90000, 4)) }} 拤谪
          </button>
        </div>
        <div class="upgrades" v-if="game.bm >= 40 || game.bn.gt(0)">
          <h3>拥谫升级</h3>
          <button @click="upgradeBn(1)" :disabled="game.bn.lt(getUpgradeCost(game.bnUpgrade1, 90, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拤谪获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bnUpgrade1)) }} (Lv.{{ game.bnUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bnUpgrade1, 90, 1)) }} 拣谩
          </button>
          <button @click="upgradeBn(2)" :disabled="game.bn.lt(getUpgradeCost(game.bnUpgrade2, 900, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拤谪经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bnUpgrade2)) }} (Lv.{{ game.bnUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bnUpgrade2, 900, 2)) }} 拣谩
          </button>
          <button @click="upgradeBn(3)" :disabled="game.bn.lt(getUpgradeCost(game.bnUpgrade3, 9000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拦谬获取 ×{{ formatNumber(getUpgrade3Effect(game.bnUpgrade3)) }} (Lv.{{ game.bnUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bnUpgrade3, 9000, 3)) }} 拣谩
          </button>
          <button @click="upgradeBn(4)" :disabled="game.bn.lt(getUpgradeCost(game.bnUpgrade4, 90000, 4))|| game.bnUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bnUpgrade4)) }} (Lv.{{ game.bnUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bnUpgrade4, 90000, 4)) }} 拣谩
          </button>
        </div>
        <div class="upgrades" v-if="game.bm >= 120 || game.bo.gt(0)">
          <h3>拦谬升级</h3>
          <button @click="upgradeBo(1)" :disabled="game.bo.lt(getUpgradeCost(game.boUpgrade1, 90, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拦谬获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.boUpgrade1)) }} (Lv.{{ game.boUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.boUpgrade1, 90, 1)) }} 拦谬
          </button>
          <button @click="upgradeBo(2)" :disabled="game.bo.lt(getUpgradeCost(game.boUpgrade2, 900, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拦谬经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.boUpgrade2)) }} (Lv.{{ game.boUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.boUpgrade2, 900, 2)) }} 拦谬
          </button>
          <button @click="upgradeBo(3)" :disabled="game.bo.lt(getUpgradeCost(game.boUpgrade3, 9000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拧谭获取 ×{{ formatNumber(getUpgrade3Effect(game.boUpgrade3)) }} (Lv.{{ game.boUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.boUpgrade3, 9000, 3)) }} 拦谬
          </button>
          <button @click="upgradeBo(4)" :disabled="game.bo.lt(getUpgradeCost(game.boUpgrade4, 90000, 4))|| game.boUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.boUpgrade4)) }} (Lv.{{ game.boUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.boUpgrade4, 90000, 4)) }} 拦谬
          </button>
        </div>
        <div class="upgrades" v-if="game.bm >= 240 || game.bp.gt(0)">
          <h3>拧谭升级</h3>
          <button @click="upgradeBp(1)" :disabled="game.bp.lt(getUpgradeCost(game.bpUpgrade1, 90, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拧谭获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bpUpgrade1)) }} (Lv.{{ game.bpUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bpUpgrade1, 90, 1)) }} 拧谭
          </button>
          <button @click="upgradeBp(2)" :disabled="game.bp.lt(getUpgradeCost(game.bpUpgrade2, 900, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拧谭经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bpUpgrade2)) }} (Lv.{{ game.bpUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bpUpgrade2, 900, 2)) }} 拧谭
          </button>
          <button @click="upgradeBp(3)" :disabled="game.bp.lt(getUpgradeCost(game.bpUpgrade3, 9000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拨谮获取 ×{{ formatNumber(getUpgrade3Effect(game.bpUpgrade3)) }} (Lv.{{ game.bpUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bpUpgrade3, 9000, 3)) }} 拧谭
          </button>
          <button @click="upgradeBp(4)" :disabled="game.bp.lt(getUpgradeCost(game.bpUpgrade4, 90000, 4))|| game.bpUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bpUpgrade4)) }} (Lv.{{ game.bpUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bpUpgrade4, 90000, 4)) }} 拧谭
          </button>
        </div>
        <div class="section">
          <h2>拤谪自动化</h2>
          <div class="auto-upgrades">
            <div v-for="i in 8" :key="i" class="auto-upgrade">
              <button @click="upgradeBjAuto(i)" :class="{ locked: !(game as any)[`bjAuto${i}Unlocked`] }" class="auto-btn">
                <img src="/baixie.png" alt="" class="btn-icon" />
                <div class="auto-title">{{ (game as any)[`bjAuto${i}Unlocked`] ? `自动化${i} 已解锁` : `解锁自动化${i}` }}</div>
                <div class="auto-desc">
                  <span v-if="i===1">每秒自动获得10%重置时获得的拤谪及其经验</span>
                  <span v-else-if="i===2">每秒自动获得10%重置时获得的拥谫</span>
                  <span v-else-if="i===3">每秒自动获得10%重置时获得的拦谬</span>
                  <span v-else-if="i===4">每秒自动获得10%重置时获得的拧谭</span>
                  <span v-else-if="i===5">自动购买拤谪升级且不消耗拤谪</span>
                  <span v-else-if="i===6">自动购买拥谫升级且不消耗拥谫</span>
                  <span v-else-if="i===7">自动购买拦谬升级且不消耗拦谬</span>
                  <span v-else>自动购买拧谭升级且不消耗拧谭</span>
                </div>
                <div class="auto-cost">
                  花费：{{ formatNumber([1e17,1e17,1e17,1e17,1e11,1e11,1e11,1e11][i-1]) }} {{ ['拤谪','拥谫','拦谬','拧谭','拤谪','拥谫','拦谬','拧谭'][i-1] }}
                </div>
              </button>
            </div>
          </div>
        </div>
      </div>
      
      <!-- 拨谮层级标签页 -->
      <div v-show="activeTab === 'boxie'" class="tab-content">
        <h2>拨谮层级</h2>
        <div class="resources">
          <div class="resource">拨谮：<span class="counter">{{ formatNumber(game.bq) }}</span></div>
          <div class="resource">经验：<span class="counter">{{ formatNumber(game.br) }}</span></div>
          <div class="resource">等级：<span class="counter">{{ game.bs }}</span></div>
          <div class="resource">择谯：<span class="counter">{{ formatNumber(game.bt) }}</span></div>
          <div class="resource">拪谰：<span class="counter">{{ formatNumber(game.bu) }}</span></div>
          <div class="resource">拫谱：<span class="counter">{{ formatNumber(game.bv) }}</span></div>
        </div>
        <div class="progress-bar" v-if="game.isBqUnlocked">
          <div class="progress-fill" :style="{ width: Math.min(game.br.div(getBsUpgradeCost(game.bs)).toNumber() * 100, 100) + '%' }">
            <span class="progress-text" style="position: absolute; left: 50%; transform: translateX(-50%);">{{ formatNumber(game.br) }} / {{ formatNumber(getBsUpgradeCost(game.bs)) }}</span>
          </div>
        </div>
        <span class="bonus" v-if="game.isBqUnlocked">上一层资源加成：×{{ formatNumber(getLevelBonus(game.bs)) }}</span>
          <button @click="clickBq(1)" class="reset-btn" :disabled="game.bm < 400">
            <img src="/baixie.png" alt="" class="btn-icon" />拨谮一次(需要 400 拤谪等级)：重置之前层级除自动化外的所有内容，获得 {{ formatNumber(getBqClickMultiplier()) }} 拨谮，{{ formatNumber(getBrClickMultiplier()) }} 经验
          </button>
          <button @click="resetBt" class="reset-btn" :disabled="game.bs < 40">
            <img src="/baixie.png" alt="" class="btn-icon" />择谯重置(需要 40 拨谮等级)：重置拨谮重置的东西、拨谮和拨谮升级，获得 {{ formatNumber(getBtResetReward()) }} 择谯
          </button>
          <button @click="resetBu" class="reset-btn" :disabled="game.bs < 120">
            <img src="/baixie.png" alt="" class="btn-icon" />拪谰重置(需要 )：获得 {{ formatNumber(getBuResetReward()) }}
          </button>
          <button @click="resetBv" class="reset-btn" :disabled="game.bs < 240">
            <img src="/baixie.png" alt="" class="btn-icon" />拫谱重置 (获得 {{ formatNumber(getBvResetReward()) }}
          </button>
        <div class="upgrades" v-if="game.isBqUnlocked">
          <h3>拨谮升级</h3>
          <button @click="upgradeBq(1)" :disabled="game.bq.lt(getUpgradeCost(game.bqUpgrade1, 270, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拨谮获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bqUpgrade1)) }} (Lv.{{ game.bqUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bqUpgrade1, 270, 1)) }} 拨谮
          </button>
          <button @click="upgradeBq(2)" :disabled="game.bq.lt(getUpgradeCost(game.bqUpgrade2, 2700, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拨谮经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bqUpgrade2)) }} (Lv.{{ game.bqUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bqUpgrade2, 2700, 2)) }} 拨谮
          </button>
          <button @click="upgradeBq(3)" :disabled="game.bq.lt(getUpgradeCost(game.bqUpgrade3, 27000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 择谯获取 ×{{ formatNumber(getUpgrade3Effect(game.bqUpgrade3)) }} (Lv.{{ game.bqUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bqUpgrade3, 27000, 3)) }} 拨谮
          </button>
          <button @click="upgradeBq(4)" :disabled="game.bq.lt(getUpgradeCost(game.bqUpgrade4, 270000, 4))|| game.bqUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bqUpgrade4)) }} (Lv.{{ game.bqUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bqUpgrade4, 270000, 4)) }} 拨谮
          </button>
        </div>
        <div class="upgrades" v-if="game.bs >= 40 || game.bt.gt(0)">
          <h3>择谯升级</h3>
          <button @click="upgradeBt(1)" :disabled="game.bt.lt(getUpgradeCost(game.btUpgrade1, 270, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拨谮获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.btUpgrade1)) }} (Lv.{{ game.btUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.btUpgrade1, 270, 1)) }} 择谯
          </button>
          <button @click="upgradeBt(2)" :disabled="game.bt.lt(getUpgradeCost(game.btUpgrade2, 2700, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拨谮经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.btUpgrade2)) }} (Lv.{{ game.btUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.btUpgrade2, 2700, 2)) }} 择谯
          </button>
          <button @click="upgradeBt(3)" :disabled="game.bt.lt(getUpgradeCost(game.btUpgrade3, 27000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拪谰获取 ×{{ formatNumber(getUpgrade3Effect(game.btUpgrade3)) }} (Lv.{{ game.btUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.btUpgrade3, 27000, 3)) }} 择谯
          </button>
          <button @click="upgradeBt(4)" :disabled="game.bt.lt(getUpgradeCost(game.btUpgrade4, 270000, 4))|| game.btUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.btUpgrade4)) }} (Lv.{{ game.btUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.btUpgrade4, 270000, 4)) }} 择谯
          </button>
        </div>
        <div class="upgrades" v-if="game.bs >= 120 || game.bu.gt(0)">
          <h3>拪谰升级</h3>
          <button @click="upgradeBu(1)" :disabled="game.bu.lt(getUpgradeCost(game.buUpgrade1, 270, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拨谮获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.buUpgrade1)) }} (Lv.{{ game.buUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.buUpgrade1, 270, 1)) }} 拪谰
          </button>
          <button @click="upgradeBu(2)" :disabled="game.bu.lt(getUpgradeCost(game.buUpgrade2, 2700, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拨谮经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.buUpgrade2)) }} (Lv.{{ game.buUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.buUpgrade2, 2700, 2)) }} 拪谰
          </button>
          <button @click="upgradeBu(3)" :disabled="game.bu.lt(getUpgradeCost(game.buUpgrade3, 27000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: 拫谱获取 ×{{ formatNumber(getUpgrade3Effect(game.buUpgrade3)) }} (Lv.{{ game.buUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.buUpgrade3, 27000, 3)) }} 拪谰
          </button>
          <button @click="upgradeBu(4)" :disabled="game.bu.lt(getUpgradeCost(game.buUpgrade4, 270000, 4))|| game.buUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.buUpgrade4)) }} (Lv.{{ game.buUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.buUpgrade4, 270000, 4)) }} 拪谰
          </button>
        </div>
        <div class="upgrades" v-if="game.bs >= 240 || game.bv.gt(0)">
          <h3>拫谱升级</h3>
          <button @click="upgradeBv(1)" :disabled="game.bv.lt(getUpgradeCost(game.bvUpgrade1, 270, 1))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 1: 拨谮获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bvUpgrade1)) }} (Lv.{{ game.bvUpgrade1 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bvUpgrade1, 270, 1)) }} 拫谱
          </button>
          <button @click="upgradeBv(2)" :disabled="game.bv.lt(getUpgradeCost(game.bvUpgrade2, 2700, 2))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 2: 拨谮经验获取 ×{{ formatNumber(getUpgrade1Or2Effect(game.bvUpgrade2)) }} (Lv.{{ game.bvUpgrade2 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bvUpgrade2, 2700, 2)) }} 拫谱
          </button>
          <button @click="upgradeBv(3)" :disabled="game.bv.lt(getUpgradeCost(game.bvUpgrade3, 27000, 3))" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 3: ？？获取 ×{{ formatNumber(getUpgrade3Effect(game.bvUpgrade3)) }} (Lv.{{ game.bvUpgrade3 }}) - 花费：{{ formatNumber(getUpgradeCost(game.bvUpgrade3, 27000, 3)) }} 拫谱
          </button>
          <button @click="upgradeBv(4)" :disabled="game.bv.lt(getUpgradeCost(game.bvUpgrade4, 270000, 4))|| game.bvUpgrade4 >= 9" class="upgrade-btn">
            <img src="/baixie.png" alt="" class="btn-icon" />
            升级 4: 自动点击速度 ×{{ formatNumber(getUpgrade4Effect(game.bvUpgrade4)) }} (Lv.{{ game.bvUpgrade4 }}/9) - 花费：{{ formatNumber(getUpgradeCost(game.bvUpgrade4, 270000, 4)) }} 拫谱
          </button>
        </div>
        <div class="section">
          <h2>拨谮自动化</h2>
          <div class="auto-upgrades">
            <div v-for="i in 8" :key="i" class="auto-upgrade">
              <button @click="upgradeBqAuto(i)" :class="{ locked: !(game as any)[`bqAuto${i}Unlocked`] }" class="auto-btn">
                <img src="/baixie.png" alt="" class="btn-icon" />
                <div class="auto-title">{{ (game as any)[`bqAuto${i}Unlocked`] ? `拨谮自动化${i} 已解锁` : `拨谮自动化${i}` }}</div>
                <div class="auto-desc">
                  <span v-if="i===1">每秒自动获得10%重置时获得的拨谮及其经验</span>
                  <span v-else-if="i===2">每秒自动获得10%重置时获得的择谯</span>
                  <span v-else-if="i===3">每秒自动获得10%重置时获得的拪谰</span>
                  <span v-else-if="i===4">每秒自动获得10%重置时获得的拫谱</span>
                  <span v-else-if="i===5">自动购买拨谮升级且不消耗拨谮</span>
                  <span v-else-if="i===6">自动购买择谯升级且不消耗择谯</span>
                  <span v-else-if="i===7">自动购买拪谰升级且不消耗拪谰</span>
                  <span v-else>自动购买拫谱升级且不消耗拫谱</span>
                </div>
                <div class="auto-cost">
                  花费：{{ formatNumber([1e18,1e18,1e18,1e18,1e12,1e12,1e12,1e12][i-1]) }} {{ ['拨谮','择谯','拪谰','拫谱','拨谮','择谯','拪谰','拫谱'][i-1] }}
                </div>
              </button>
            </div>
          </div>
        </div>
      </div>
      
      <!-- 拜谢之神标签页 -->
      <div v-show="activeTab === 'god'" class="tab-content">
        <div class="section god-section" v-if="game.isBwUnlocked">
          <h2>拜谢之神</h2>
          <div class="god-content">
            <span class="bonus"><img src="/baixie.png" alt="" class="btn-icon" />。</span>
            <span class="bonus">不过，我们不希望每升一个层级都会重置之前层级除自动化外的所有内容。</span>
            <span class="bonus">不过你可以拜谢拜谢之神，它将重置之前所有东西。</span>
            <span class="bonus">并给之前所有东西提供加成。</span>
            <img src="/baixie.png" alt="" class="god-img" />
            <div class="god-resources">
              <div class="resource">拜谢之神：<span class="counter">{{ formatNumber(game.bw) }}</span></div>
              <div class="resource">全局加成：×{{ formatNumber(getBwBonus()) }}</div>
            </div>
            <button @click="resetGod" class="god-reset-btn"><img src="/baixie.png" alt="" class="btn-icon" />重置之前所有东西，获得{{ formatNumber(getBwResetReward()) }}拜谢之神</button>
          </div>
        </div>
      </div>
      
      <!-- 存档系统标签页 -->
      <div v-show="activeTab === 'save'" class="tab-content">
        <div class="section save-section">
          <h2>存档系统</h2>
          <div class="save-buttons">
            <button @click="exportSave" class="save-btn"><img src="/baixie.png" alt="" class="btn-icon" />导出存档</button>
            <label class="save-btn"><img src="/baixie.png" alt="" class="btn-icon" />导入存档<input type="file" @change="importSave" accept=".txt" style="display: none" /></label>
            <button @click="hardReset" class="reset-btn danger"><img src="/baixie.png" alt="" class="btn-icon" />硬重置</button>
            <!--a href="https://www.bilibili.com/video/BV1GJ411x7h7" target="_blank" class="save-btn completion-btn"><img src="/baixie.png" alt="" class="btn-icon" />通关这个游戏</a-->
          <span class="bonus">游戏说明：所有的升级1和2每10级效果乘以2，每100级效果乘以10。所有的升级3每10级效果乘以1.414，每100级效果乘以3.162。当前层级的等级到达400解锁下一层级。</span>
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
  max-width: 1024px;
  margin: 0 auto;
  background: transparent;
  border-radius: 10px;
  padding: 20px;
}
.tab-navigation {
  display: flex;
  gap: 10px;
  justify-content: center;
  flex-wrap: wrap;
  margin-bottom: 20px;
  padding: 10px;
  background: rgba(255, 215, 0, 0.2);
  border-radius: 10px;
  border: 2px solid rgba(255, 215, 0, 0.5);
}
.tab-btn {
  padding: 10px 20px;
  font-size: 16px;
  background: linear-gradient(135deg, #ffd700 0%, #ffaa00 100%);
  color: #000;
  border: 2px solid #ffd700;
  border-radius: 10px;
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
  margin-bottom: 20px;
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

.main-btn, .upgrade-btn, .reset-btn, .save-btn, .auto-btn, .god-reset-btn {
  padding: 12px 16px;
  font-size: 16px;
  background: linear-gradient(135deg, #ffd700 0%, #ffaa00 100%);
  color: #000;
  border: 2px solid #ffd700;
  border-radius: 10px;
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
  margin-bottom: 5px;
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
  background: rgba(255, 215, 0, 0.6);
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
  width: 512px;
  height: 512px;
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
