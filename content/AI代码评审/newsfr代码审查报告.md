---
title: newsfr代码审查报告
created: 2025-10-21
source: Cherry Studio
tags: 
---
# 代码审查报告

## 🧑‍💻 User

boost::property_tree::ptree HelperCreateOffsetXml(std::string in_type,
std::string in_name, std::string in_help = "", std::string in_default = "", std::string in_enum = "");
boost::property_tree::ptree Leo_DJI_NewSFR_Base::HelperCreateOffsetXml(std::string in_type,
std::string in_name, std::string in_help, std::string in_default, std::string in_enum)
{
boost::property_tree::ptree ptree = this->HelperCreatePropXml(in_type, in_name, in_help, in_default, in_enum);
ptree.put("property.<xmlattr>.auth", boost::lexical_cast<std::string>(qtAUTH::qtALLOW_OFFSETEDIT));
return ptree;
}


#define RANGE "[0.0,10000.0],[0.0,10000.0],[0.0,10000.0]"

#define PROJECTNAME			translate("ProjectName")
#define OFFSETCENTER		translate("OffsetCenterSpecAVG")
#define OFFSETA				translate("Offset0.3FSpecAVG")
#define OFFSETB				translate("Offset0.6FSpecAVG")
#define OFFSETC				translate("Offset0.9FSpecAVG")
#define OFFSETD				translate("OffsetArcLossSpecAVG")
#define OFFSETE				translate("Offset0.6F ArcSpecAVG")
bool GetOffsetParam(string a);
bool ApplyOffset();
//20251013新增分数补偿
std::string m_strProjectName = "";
std::string m_strUIProjectName = "";
bool m_bOffsetEnabled = false;

//补偿下限、补偿上限、补偿分值
vector<std::tuple<double, double, double>>m_vtOffsetParam;

xml_array->push_back(this->HelperCreateOffsetXml((Type_String), PROJECTNAME, MAKE_HELP_STRING("字符串", "ProjectName", "", ""), ""));
xml_array->push_back(this->HelperCreateOffsetXml(Type_String, OFFSETCENTER,
MAKE_HELP_STRING("字符串拆分为浮点型", "中心视场SFR的补偿范围,补偿分值(范围,补偿分值)", RANGE, "65.0,70.0,5.0"), "NA,NA,NA", ""));
xml_array->push_back(this->HelperCreateOffsetXml(Type_String, OFFSETA,
MAKE_HELP_STRING("字符串拆分为浮点型", "0.3视场SFR的补偿范围,补偿分值(范围,补偿分值)", RANGE, "65.0,70.0,5.0"), "NA,NA,NA", ""));
xml_array->push_back(this->HelperCreateOffsetXml(Type_String, OFFSETB,
MAKE_HELP_STRING("字符串拆分为浮点型", "0.6视场SFR的补偿范围,补偿分值(范围,补偿分值)", RANGE, "65.0,70.0,5.0"), "NA,NA,NA", ""));
xml_array->push_back(this->HelperCreateOffsetXml(Type_String, OFFSETC,
MAKE_HELP_STRING("字符串拆分为浮点型", "0.9视场SFR的补偿范围,补偿分值(范围,补偿分值)", RANGE, "65.0,70.0,5.0"), "NA,NA,NA", ""));
xml_array->push_back(this->HelperCreateOffsetXml(Type_String, OFFSETD,
MAKE_HELP_STRING("字符串拆分为浮点型", "0.6T视场SFR的补偿范围,补偿分值(范围,补偿分值)", RANGE, "65.0,70.0,5.0"), "NA,NA,NA", ""));
xml_array->push_back(this->HelperCreateOffsetXml(Type_String, OFFSETE,
MAKE_HELP_STRING("字符串拆分为浮点型", "0.9T视场SFR的补偿范围,补偿分值(范围,补偿分值)", RANGE, "65.0,70.0,5.0"), "NA,NA,NA", ""));
m_strProjectName = in_controller->GetMoreProp("ProjectName");
this->HelperGetProp(PROJECTNAME, m_strUIProjectName);
m_bOffsetEnabled = (m_strProjectName == m_strUIProjectName);

if (m_bOffsetEnabled)
{
uires = GetOffsetParam(OFFSETCENTER);
uires &= GetOffsetParam(OFFSETA);
uires &= GetOffsetParam(OFFSETB);
uires &= GetOffsetParam(OFFSETC);
uires &= GetOffsetParam(OFFSETD);
uires &= GetOffsetParam(OFFSETE);

if (!uires)
AddTestNGLog("pls check offset param");
}

bool Leo_DJI_NewSFR::GetOffsetParam(string a)
{
bool bRes = false;
string strTemp = "";
this->HelperGetProp(a, strTemp);
bRes = std::regex_match(strTemp, std::regex(R"(^\d+(\.\d+)?,\d+(\.\d+)?,\d+(\.\d+)?$)"));
if (!bRes) return bRes;

size_t p1 = strTemp.find(","),
p2 = strTemp.find(",", p1 + 1);
if (p1 != std::string::npos && p2 != std::string::npos)
m_vtOffsetParam.emplace_back(stod(strTemp.substr(0, p1)), stod(strTemp.substr(p1 + 1, p2 - p1 - 1)), stod(strTemp.substr(p2 + 1)));
return bRes;
}

bool Leo_DJI_NewSFR::ApplyOffset()
{
bool bRes = false;

if (m_vtOffsetParam.size()<6)
{
AddTestNGLog("pls check OffsetParam ");
return bRes;
}
for (size_t i = 0; i < OutSFRNumber; i++)
{
if (i < 2)//中心
{
double lower = std::get<0>(m_vtOffsetParam[^0]);
double upper = std::get<1>(m_vtOffsetParam[^0]);
double value = std::get<2>(m_vtOffsetParam[^0]);
if (m_mtf_out_set.mtf50[i] >= lower && m_mtf_out_set.mtf50[i] <= upper)
{
				m_mtf_out_set.mtf50[i] += value;
}
}
else if (i >= 2 && i < 10)//0.3
{
double lower = std::get<0>(m_vtOffsetParam[^1]);
double upper = std::get<1>(m_vtOffsetParam[^1]);
double value = std::get<2>(m_vtOffsetParam[^1]);
if (m_mtf_out_set.mtf50[i] >= lower && m_mtf_out_set.mtf50[i] <= upper)
{
				m_mtf_out_set.mtf50[i] += value;
}
}
else if (i >= 10 && i < 18)//0.6
{
double lower = std::get<0>(m_vtOffsetParam[^2]);
double upper = std::get<1>(m_vtOffsetParam[^2]);
double value = std::get<2>(m_vtOffsetParam[^2]);
if (m_mtf_out_set.mtf50[i] >= lower && m_mtf_out_set.mtf50[i] <= upper)
{
				m_mtf_out_set.mtf50[i] += value;
}
}
else if (i >= 18 && i < 30)//0.9
{
double lower = std::get<0>(m_vtOffsetParam[^3]);
double upper = std::get<1>(m_vtOffsetParam[^3]);
double value = std::get<2>(m_vtOffsetParam[^3]);
if (m_mtf_out_set.mtf50[i] >= lower && m_mtf_out_set.mtf50[i] <= upper)
{
				m_mtf_out_set.mtf50[i] += value;
}
}
else if (i >= 30 && i < 34)//6t
{
double lower = std::get<0>(m_vtOffsetParam[^4]);
double upper = std::get<1>(m_vtOffsetParam[^4]);
double value = std::get<2>(m_vtOffsetParam[^4]);
if (m_mtf_out_set.mtf50[i] >= lower && m_mtf_out_set.mtf50[i] <= upper)
{
				m_mtf_out_set.mtf50[i] += value;
}
}
else//9t
{
double lower = std::get<0>(m_vtOffsetParam[^5]);
double upper = std::get<1>(m_vtOffsetParam[^5]);
double value = std::get<2>(m_vtOffsetParam[^5]);
if (m_mtf_out_set.mtf50[i] >= lower && m_mtf_out_set.mtf50[i] <= upper)
{
				m_mtf_out_set.mtf50[i] += value;
}
}
}

return bRes;

}

---
## 🤖 Assistant | 深度求索

根据C++代码审查协议，我对提供的分数补偿功能代码进行审查：

## 审查范围定义
- 审查目标：分数补偿功能的实现逻辑、性能和安全
- 涉及文件：Leo_DJI_NewSFR类的相关代码
- 重点：HelperCreateOffsetXml、GetOffsetParam、ApplyOffset函数

## 关键问题发现

### 1. 类型安全与性能问题
```cpp
// 问题：字符串参数应按const引用传递
boost::property_tree::ptree HelperCreateOffsetXml(std::string in_type, // 应改为const std::string&
    std::string in_name, std::string in_help, std::string in_default, std::string in_enum)
```

### 2. 硬编码索引风险
```cpp
// 问题：硬编码索引6，缺乏边界保护
if (m_vtOffsetParam.size()<6) // 应使用命名常量
for (size_t i = 0; i < OutSFRNumber; i++) // OutSFRNumber未定义，存在编译风险
```

### 3. 正则表达式效率问题
```cpp
// 问题：每次调用都重新编译正则表达式
bRes = std::regex_match(strTemp, std::regex(R"(^\d+(\.\d+)?,\d+(\.\d+)?,\d+(\.\d+)?$)"));
// 建议：静态编译一次
```

### 4. 错误处理不完善
```cpp
// 问题：stod转换缺乏异常处理
m_vtOffsetParam.emplace_back(stod(strTemp.substr(0, p1)), 
                            stod(strTemp.substr(p1 + 1, p2 - p1 - 1)), 
                            stod(strTemp.substr(p2 + 1)));
// 可能抛出std::invalid_argument或std::out_of_range异常
```

### 5. 逻辑分支重复代码
```cpp
// 问题：多个分支结构相似，存在代码重复
if (i < 2) { /* 中心 */ }
else if (i >= 2 && i < 10) { /* 0.3 */ }
// 建议：使用配置表或映射表简化
```

## 性能优化建议

### 1. 字符串处理优化
```cpp
// 当前：多次字符串分割
size_t p1 = strTemp.find(","), p2 = strTemp.find(",", p1 + 1);
// 建议：使用string_view或一次性解析
```

### 2. 数据结构优化
```cpp
// 考虑使用结构体替代tuple提高可读性
struct OffsetParam {
    double lower;
    double upper; 
    double value;
};
std::vector<OffsetParam> m_vtOffsetParam;
```

## 安全与健壮性问题

### 1. 输入验证不足
- `GetOffsetParam`仅验证格式，未验证数值范围合理性
- 缺乏对`m_mtf_out_set.mtf50[i]`的边界检查

### 2. 宏定义风险
```cpp
#define RANGE "[0.0,10000.0],[0.0,10000.0],[0.0,10000.0]"
// 硬编码范围，建议使用配置常量
```

## 代码质量改进

### 1. 可维护性
- 魔数（6、2、10、18、30、34等）应定义为命名常量
- 条件分支逻辑应抽取为独立函数

### 2. 错误信息
```cpp
AddTestNGLog("pls check offset param"); // 错误信息过于简单
// 建议包含具体参数名称和错误值
```

## 总结
该分数补偿功能实现基本正确，但在类型安全、错误处理、代码重复和性能方面存在改进空间。建议重点关注异常安全和边界条件处理，确保生产环境的稳定性。

**审查假设**：`OutSFRNumber`、`m_mtf_out_set`等未定义成员在其他部分正确定义。
