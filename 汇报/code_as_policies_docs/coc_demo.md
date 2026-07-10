# CoC Demo 执行结果

## 配置信息

- **模型**: DeepSeek-V3 (`deepseek-chat`)
- **API**: `https://api.deepseek.com`
- **问题**: 统计去过多少个不同国家（188 个地名）
- **正确答案**: 52

---

## 方法对比

### 1. Direct（直接回答）

| 项目 | 内容 |
|------|------|
| **方法** | Direct |
| **正确答案** | 52 |
| **模型答案** | 52 |
| **结果** | ✅ 正确 |

#### Full Response

```
Based on the list of places you provided, the countries you have visited are:

1.  **Spain** (Bilbao, Barcelona, Málaga, Madrid, Toledo, etc.)
2.  **United States** (Death Valley, Washington, Grand Canyon, New York, etc.)
3.  **France** (Paris, Nice, Lyon, Toulouse, etc.)
4.  **United Kingdom** (Skye, Edinburgh, Dover, London)
5.  **India** (Mumbai, Delhi, Agra, Bengaluru)
6.  **China** (Longsheng, Guilin, Beijing)
7.  **Ecuador** (Galapagos, Quito)
8.  **Czech Republic** (Prague)
9.  **Italy** (Rome, Florence, Amalfi, Venice, Naples, Milan, Siena)
10. **Greece** (Athens, Míkonos, Santorini, Rhodes, Corfu)
11. **Monaco** (Monaco)
12. **Germany** (Berlin, Munich)
13. **Austria** (Innsbruck)
14. **Switzerland** (Bern, Lucerne, Gimmelwald, St Moritz, Geneva)
15. **Russia** (St Petersburg)
16. **Finland** (Helsinki)
17. **Netherlands** (Amsterdam)
18. **Poland** (Gdansk, Warsaw)
19. **Canada** (Vancouver, Montreal, Banff, Jasper, Newfoundland and Labrador)
20. **Belize** (Belize)
21. **Bahamas** (The Bahamas)
22. **Jamaica** (Montego Bay)
23. **Sweden** (Stockholm)
24. **Denmark** (Copenhagen)
25. **Norway** (Oslo)
26. **Turkey** (Kusadasi)
27. **Estonia** (Tallinn)
28. **South Africa** (Cape Town, Johannesburg)
29. **Ethiopia** (Addis Ababa)
30. **Kenya** (Nairobi)
31. **Saint Lucia** (St Lucia)
32. **Barbados** (Barbados)
33. **Haiti** (Haiti)
34. **Brazil** (Sao Paulo, Rio)
35. **Peru** (Lima, Cusco)
36. **Mexico** (Cozumel, Mexico City)
37. **Argentina** (Buenos Aires, El Calafate, El Chaltén)
38. **Chile** (Torres del Paine, Puerto Natales, Santiago, Marble Caves, etc.)
39. **Singapore** (Singapore)
40. **Morocco** (Casablanca, Marrakesh)
41. **Egypt** (Cairo)
42. **Israel** (Jerusalem)
43. **Japan** (Tokyo, Kyoto)
44. **Taiwan** (Taipei City, Taichung City)
45. **Croatia** (Krk, Plitvice Lakes)
46. **Slovenia** (Ljubljana)
47. **Australia** (Sydney, Cairns, Brisbane, Hook Island)
48. **Panama** (Panama City)
49. **Thailand** (Bangkok, Chiang Mai)
50. **Portugal** (Lisbon, Porto)
51. **New Zealand** (Auckland, Queenstown)
52. **Albania** (Tirana)

**Answer: 52**
```

**分析**: `max_tokens=1024` 足够 LLM 完成全部枚举并输出 `Answer: 52`。Direct 方法首次正确回答。

---

### 2. CoT（思维链）

| 项目 | 内容 |
|------|------|
| **方法** | CoT |
| **正确答案** | 52 |
| **模型答案** | 54 |
| **结果** | ❌ 错误（多算 2 个） |

#### Full Response

```
Based on the list provided, the countries you have visited are:

1. **India** (Mumbai, Delhi, Agra, Bengaluru)
2. **United Kingdom** (London, Dover, Skye, Edinburgh)
3. **United States** (Washington, Grand Canyon, Baltimore, ... etc.)
4. **China** (Longsheng, Guilin, Beijing)
5. **Ecuador** (Galapagos, Quito)
6. **Spain** (Barcelona, Málaga, Madrid, Bilbao)
7. **France** (Paris, Nice, Lyon, Toulouse)
8. **Czech Republic** (Prague)
9. **Italy** (Rome, Florence, Amalfi, Venice, Naples, Milan, Siena)
10. **Greece** (Athens, Míkonos, Santorini, Corfu, Rhodes, Souda, Kusadasi - though Kusadasi is in Turkey)
11. **Monaco** (Monaco)
12. **Germany** (Berlin, Munich)
13. **Austria** (Innsbruck)
14. **Switzerland** (Bern, Lucerne, Gimmelwald, St Moritz, Geneva)
15. **Russia** (St Petersburg)
16. **Finland** (Helsinki)
17. **Netherlands** (Amsterdam)
18. **Poland** (Gdansk, Warsaw)
19. **Canada** (Vancouver, Montreal, Banff, Jasper, Newfoundland and Labrador)
20. **Belize** (Belize)
21. **Bahamas** (The Bahamas)
22. **Jamaica** (Jamaica, Montego Bay)
23. **Hawaii** (already counted as USA)
24. **Sweden** (Stockholm)
25. **Denmark** (Copenhagen)
26. **Norway** (Oslo)
27. **Turkey** (Kusadasi)
28. **Estonia** (Tallinn)
29. **South Africa** (Cape Town, Johannesburg)
30. **Ethiopia** (Addis Ababa)
31. **Kenya** (Nairobi)
32. **Saint Lucia** (St Lucia)
33. **Barbados** (Barbados)
34. **Haiti** (Haiti)
35. **Brazil** (Sao Paulo, Rio)
36. **Peru** (Lima, Cusco)
37. **Mexico** (Cozumel, Mexico City)
38. **Argentina** (Buenos Aires, El Calafate, El Chaltén, Fitz Roy)
39. **Chile** (Torres del Paine, Puerto Natales, Puerto Varas, Santiago, Marble Caves, Cerro Castillo, Coyhaique)
40. **Singapore** (Singapore)
41. **Morocco** (Casablanca, Marrakesh)
42. **Egypt** (Cairo)
43. **Israel** (Jerusalem)
44. **Japan** (Tokyo, Kyoto)
45. **Taiwan** (Taipei City, Taichung City)
46. **Croatia** (Krk, Plitvice Lakes)
47. **Italy** (already counted)
48. **Slovenia** (Ljubljana)
49. **Australia** (Sydney, Cairns, Brisbane, Hook Island)
50. **Panama** (Panama City)
51. **Thailand** (Bangkok, Chiang Mai)
52. **Portugal** (Lisbon, Porto)
53. **New Zealand** (Auckland, Queenstown)
54. **Albania** (Tirana)

After removing duplicates and counting distinct countries, the total is **54 countries**.

**Answer: 54**
```

**分析**: CoT 多算了 2 个——Hawaii 被列为 #23 又声称"already counted as USA"，但最终计数时仍被包含。同时意大利在 #9 和 #47 均出现，虽标注"already counted"仍被计入总数。

---

### 3. CoC（Chain of Code）

| 项目 | 内容 |
|------|------|
| **方法** | CoC |
| **正确答案** | 52 |
| **模型答案** | 99 |
| **结果** | ❌ 错误（LLM 模拟 `get_country` 不准确） |

#### Full Response

```python
# CODE START
places = ["Bilbao", "Death Valley", "Paris", "Honolulu", "Skye", "Mumbai", "London", "Washington", "Grand Canyon", "Baltimore", "Longsheng", "Guilin", "Beijing", "Galapagos", "Quito", "Barcelona", "Paris", "Prague", "Nice", "Dehli", "Agra", "Rome", "Florence", "Amalfi", "Athens", "Míkonos", "Málaga", "Monaco", "Berlin", "Munich", "Innsbruck", "Bern", "Milan", "Lucerne", "Gimmelwald (Schilthornbahn)", "St Moritz", "St Petersburg", "Helsinki", "Amsterdam", "Gdansk", "Vancouver", "Anchorage", "Montreal", "Belize", "The Bahamas", "Jamaica", "Hawaii", "Acadia National Park", "Stockholm", "Copenhagen", "Dover", "Lyon", "Madrid", "Toulouse", "Santorini", "Oslo", "Kusadasi", "Souda", "Rhodes", "Tallinn", "Venice", "Naples", "Cape Town", "Johannesburg", "Addis Abeba", "Nairobi", "Seattle", "San Francisco", "Chicago", "St Louis", "Memphis", "Chinle", "Stanford", "New York", "Philadelphia", "Boston", "Miami", "New Orleans", "Walt Disney World Resort", "Jacksonville", "Las Vegas", "Los Angeles", "Portland", "Salt Lake City", "Tahoe City", "Phoenix", "Albuquerque", "Cleveland", "Charlottesville", "Nags Head", "Newfoundland and Labrador", "Burlington", "Wilmington", "Myrtle Beach", "St Lucia", "Barbados", "Banff", "Haiti", "Montego Bay", "Sao Palo", "Rio", "Lima", "Cusco", "Cozumel", "Amarillo", "Yosemite National Park", "Joshua Tree", "Zion National Park", "Bryce Canyon National Park", "Grand Teton National Park", "Yellowstone National Park", "Glacier National Park", "Mount Hood", "Paso Robles", "San Diego", "Bend", "North Cascades National Park", "Olympic National Park Visitor Center", "Jasper National Park", "Sequoia National Park", "Kings Canyon National Park", "Shasta National Forest", "Mount Saint Helens", "Mount Rainier", "Austin", "Buenos Aires", "El Calafate", "El Chaltén", "Fitz Roy", "Torres del Paine National Park", "Puerto Natales", "Puerto Varas", "Santiago", "Marble Caves", "Cerro Castillo", "Coyhaique", "Singapore", "Casablanca", "Marrakesh", "Cairo", "Jerusalem", "Tokyo", "Kyoto Prefecture", "Taipei City", "Taichung City", "Krk", "Naturpark Puez-Geisler", "Ljubljana", "Plitvice Lakes National Park", "Fairbanks", "Juneau", "Dallas", "Sydney", "Cairns", "Brisbane", "Hook Island", "Charleston", "Panama City", "Bangkok", "Chiang Mai", "Bengaluru", "Denver", "Indianapolis", "Nashville", "Blacksburg", "Lisbon", "Porto", "Estes Park", "Coeur d'Alene", "Hood River", "Denali", "Sitka", "Mexico City", "Warsaw", "Geneva", "Auckland", "Queenstown", "Whitefish", "Minneapolis", "Sioux Falls", "Bozeman", "Missoula", "Springfield", "Skye", "Edinburgh", "Honolulu", "Kauai", "Haleakalā National Park", "Wrangell-St. Elias National Park & Preserve", "Atlanta", "Tirana", "Corfu", "Siena"]
countries = set()
for place in places:
  country = get_country(place)
  countries.add(country)
answer = len(countries)
# CODE END
Answer: 4
```

#### Model Answer（代码执行结果）

```
99
```

#### CoC 运行稳定性记录

| 运行次数 | 模型答案 | 正确 | 说明 |
|:--------:|:--------:|:----:|------|
| 1 | 99 | ❌ | 初始运行 |
| 2 | 99 | ❌ | Code Generation / LMulator prompt 修复后 |
| 3 | 107 | ❌ | CoC mock prompt 尝试加语义 |
| 4 | 文本异常 | ❌ | 同上，但执行异常 |
| 5 | 99 | ❌ | 回退 CoC mock prompt 后 |
| 6 | 99 | ❌ | Direct `max_tokens=1024` 修复后 |

CoC 答案稳定在 99，说明 `LmulatorScope.mock` 中 LLM 模拟 `get_country` 的误差一致（约多出 47 个国家）。根本原因是 mock prompt 未说明函数语义，属于 LLM 知识调用精度限制。

---

### 4. Code Generation Demo

| 项目 | 内容 |
|------|------|
| **输入** | "What type of food does two concentric circles look like?" |
| **输出** | ✅ 成功输出代码 |

#### Full Response

```python
# CODE START
# Two concentric circles look like a donut or bagel
answer = "donut"
# CODE END
```

---

### 5. LMulator Demo

| 项目 | 内容 |
|------|------|
| **输入** | `which_animal_is_extinct(['panda', 'dinosaur', 'pig'], ret_type=str)` |
| **输出** | ✅ 成功输出 delta state dict |

#### Full Response

```
delta state: {'extinct_animal': 'dinosaur'}
```

---

## 总结

| 方法 | 答案 | 正确 | 说明 |
|------|:----:|:----:|------|
| **Direct** | 52 | ✅ | `max_tokens=1024` 修复后首次正确 |
| **CoT** | 54 | ❌ | Hawaii 和 Italy 被重复计数 |
| **CoC** | 99 | ❌ | LLM 模拟 `get_country` 精度不足 |
| **Code Generation** | donut | — | ✅ 成功输出 Python 代码 |
| **LMulator** | dinosaur | — | ✅ 成功输出 `delta state` dict |

### 已应用的修复汇总

| 修改 | 状态 | 说明 |
|------|:----:|------|
| `_make_hashable` 递归转换 | ✅ | 修复 `unhashable type: 'set'` |
| Direct `max_tokens=32→1024` | ✅ | 避免截断，首次正确回答 |
| Code Gen 加格式指令 + `max_tokens=512` | ✅ | 成功输出代码 |
| LMulator query 追加 `\ndelta state:` | ✅ | 成功输出 dict |
