---
title: Leo_LetterChart代码审查
created: 2025-10-27
source: Cherry Studio
tags: 
---
# Leo_LetterChart代码审查

## 🧑‍💻 User

#pragma once
#include "../ChartCheckBase.h"


class Leo_LetterChart :public ChartCheckBase
{


enum ErrorCode :char
{
ERROR_OK = 0, //测试pass
ERROR_IMGEMPTY = -1, //图像数据为空
ERROR_WRONGSIZE = -2, //错误的size
ERROR_NOTFINDROI = -3, //无法找到上下格子
ERROR_WRONGROISIZE = -4, //错误的格子数量
ERRPR_UNKONW = -5, // 未知错误
};



typedef struct {
void* m_pImage;
int m_iWidth;
int m_iHeight;
double m_dCannyThresholdH;
double m_dCannyThresholdL;
double m_dMinLineRatio;
double m_dMaxLineCap;
bool m_bDebugMode;
}CHECK_INPUT_INFO;

typedef struct {
double m_dMeanAngle;
double m_dMeanDistance;
double m_dCrossPoint[^2];
}CHECK_OUTPUT_INFO;

//extern "C" int __declspec(dllexport) LetterChartCheckCale(CHECK_INPUT_INFO &m_Input, CHECK_OUTPUT_INFO &m_Output);

public:
Leo_LetterChart();
virtual ~Leo_LetterChart();
virtual void GetProperty(qtmPropArray&) override;
protected:
virtual bool GetUIParam() override;
virtual bool Run() override;
virtual bool LoadLibraryDLL() override;//重写子类的加载库
virtual bool FreeLibraryDLL() override;//重写子类的加载库

virtual bool OutPutPDF() override;//重写子类的加载库
virtual bool OutPutMes() override;

//加载dll
typedef bool(*LetterChartCheckCale)(CHECK_INPUT_INFO &m_Input, CHECK_OUTPUT_INFO &m_Output);

LetterChartCheckCale m_LetterChartTestProg;

string m_strCannyThresholdSpec = "";
double m_dCannyThresholdSpecH = 0.0;
double m_dCannyThresholdSpecL = 0.0;

double m_dMinLineLengthRatio = 0.0;

Pair2d m_dAngleSpec{ 0.0, 0.0 };
Pair2d m_dWidthSpec{ 0.0, 0.0 };
Pair2d m_dXYDiffSpec{ 0.0, 0.0 };

bool m_bDebugMode = false;

CHECK_INPUT_INFO m_inputInfo;
CHECK_OUTPUT_INFO m_outInfo;


HINSTANCE m_hLetterChartCheckDll;


//读取到的图像
cv::Mat m_Image;

//存储输出结果
std::vector<std::pair<std::string, std::string>> m_vecLetterChartUI;

//结果、名称、下限、数值、上限、Mes的上下限卡控方式
std::vector<std::tuple<std::string, std::string, std::string, std::string, std::string, ENUM_THRESHOLD_TYPE>> m_vecLetterChartOutputPDF;


};
#include "qtmDatatype.h"
#include "Leo_LetterChart.h"
#include "../../qtmALGO_Leo_ChartCheck.h"

#pragma execution_character_set("utf-8")

#define CANNYTHRESHOLDHL		translate("CannyThresholdHL")
#define MINLINELENGTHRATIO	translate("MinLineLengthRatio")

#define ANGLESPEC							translate("AngleSpec")
#define WIDTHSPEC							translate("WidthSpec")
#define XYDIFFSPEC						translate("XYDiffSpec")

#define DEBUGMODE						translate("DebugMode")

#define MaxLineCapNum		10


Leo_LetterChart::Leo_LetterChart()
{

}

Leo_LetterChart::~Leo_LetterChart()
{

}

/**
* @brief 获取字母图表测试的属性配置数组
*
* 该函数负责构建字母图表测试模块的用户界面属性配置，向属性数组中添加测试所需的各项参数配置，
* 包括参数类型、名称、帮助信息和默认值等。这些配置将在用户界面中显示为可配置的参数项。
*
* @param[out] xml_array - 输出参数，用于存储属性配置的数组引用
* 函数将向该数组中添加字母图表测试特有的属性配置项
*
* @note 函数配置的属性项包括：
* 1. 属性分类标识 - 标识为"LetterChart"测试类别
* 2. Canny边缘检测阈值 - 高低阈值参数，字符串类型，需拆分为两个浮点数
* 3. 最小线段长度比例 - 用于计算过滤短直线阈值的比例参数，浮点型
* 4. 角度规格 - 角度测试项的上下限规格，字符串类型，需拆分为两个浮点数
* 5. 宽度规格 - 线宽测试项的上下限规格，字符串类型，需拆分为两个浮点数
* 6. XY差异规格 - XY方向偏移测试项的上下限规格，字符串类型，需拆分为两个浮点数
* 7. 调试模式 - 布尔类型，用于控制是否启用调试模式
*
* @details 各属性项的配置说明：
* - 所有规格参数都使用字符串类型，要求用户按"下限,上限"格式输入
* - 每个属性都包含详细的帮助字符串，说明参数用途、格式和示例
* - Canny阈值建议低阈值取高阈值的1/2到1/3
* - 规格参数默认使用双精度浮点数的范围约束
* - 调试模式使用布尔类型，控制测试过程中的调试信息输出
*/
void Leo_LetterChart::GetProperty(qtmPropArray& xml_array)
{
// 在基类的基础上增加其他参数的配置
xml_array->push_back(this->HelperCreatePropXml(("PropertyCategory"), "", "", gettext("LetterChart")));

xml_array->push_back(this->HelperCreatePropXml(Type_String, CANNYTHRESHOLDHL,
MAKE_HELP_STRING("字符串 , 拆分后是浮点型", "边缘检测的高低阀值（建议低阈值取高阈值的1/2到1/3）", "", "70,35"), "NA", "", string(DEFAULT_RANGE_DOUBLE) + DEFAULT_RANGE_DOUBLE));

xml_array->push_back(this->HelperCreatePropXml(Type_Float, MINLINELENGTHRATIO,
MAKE_HELP_STRING("浮点型", "计算过滤短直线阈值的比例", "", "0.5"), to_string(INT_MAX)));

xml_array->push_back(this->HelperCreatePropXml(Type_String, ANGLESPEC,
MAKE_HELP_STRING("字符串 , 拆分后是浮点型", "角度的规格。填写格式:规格下限，规格上限", "", "4.5,5.5或-5.5, -4.5"), "NA", "", string(DEFAULT_RANGE_DOUBLE) + DEFAULT_RANGE_DOUBLE));
xml_array->push_back(this->HelperCreatePropXml(Type_String, WIDTHSPEC,
MAKE_HELP_STRING("字符串 , 拆分后是浮点型", "线宽的规格。填写格式:规格下限，规格上限", "", "4.5,5.5或-5.5, -4.5"), "NA", "", string(DEFAULT_RANGE_DOUBLE) + DEFAULT_RANGE_DOUBLE));
xml_array->push_back(this->HelperCreatePropXml(Type_String, XYDIFFSPEC,
MAKE_HELP_STRING("字符串 , 拆分后是浮点型", "XY差异的上下限。填写格式:规格下限，规格上限", "", "-80,80"), "NA", "", string(DEFAULT_RANGE_DOUBLE) + DEFAULT_RANGE_DOUBLE));

xml_array->push_back(this->HelperCreatePropXml(Type_BOOL, DEBUGMODE,
MAKE_HELP_STRING("Bool类型", "DebugMode", "", "0.5"), to_string(INT_MAX)));
}

/**
* @brief 获取并验证用户界面参数
*
* 该函数负责从用户界面获取字母图表测试所需的各项参数，并进行格式验证和合理性检查，
* 主要完成以下功能：
* 1. 获取图表模式参数
* 2. 获取Canny边缘检测阈值参数并进行正则表达式验证
* 3. 获取最小线段长度比例参数
* 4. 对角度、宽度、XY偏移等规格参数进行合理性检查
* 5. 获取调试模式参数
* 6. 将所有参数保存到界面参数向量中
*
* @return bool - 参数获取和验证结果
* true: 所有参数获取成功且通过验证
* false: 任一参数获取失败或验证不通过
*
* @note 函数执行流程：
* 1. 使用HelperGetProp获取各项界面参数
* 2. 对Canny阈值参数进行正则表达式格式验证
* 3. 解析Canny阈值字符串为具体数值
* 4. 调用CheckSpecRationality检查各规格参数的合理性
* 5. 继承基类的界面参数
* 6. 获取调试模式参数并转换为字符串格式
*
* @details 参数验证规则：
* - Canny阈值格式必须符合"数字,数字"的正则表达式模式
* - 角度规格检查使用容差5
* - 宽度规格检查使用容差5
* - XY偏移规格检查使用容差160
* - 所有参数都会保存到m_vecLetterChartUI向量中供界面显示使用
* - 验证失败时会记录详细的错误日志信息
*/
bool Leo_LetterChart::GetUIParam()
{
bool bRes = false;
string t_strUI = "";
this->HelperGetProp(CHARTPATTERN, t_strUI);
m_vecLetterChartUI.emplace_back(std::make_pair(CHARTPATTERN, t_strUI));

this->HelperGetProp(CANNYTHRESHOLDHL, m_strCannyThresholdSpec);
m_vecLetterChartUI.emplace_back(std::make_pair(CANNYTHRESHOLDHL, m_strCannyThresholdSpec));

vector<string> vec_temp;
string reg("[-]?[0-9]+[\\.]?[0-9]*?,[-]?[0-9]+[\\.]?[0-9]*?");
boost::regex regex_test(reg);
boost::smatch match;
if (!boost::regex_match(m_strCannyThresholdSpec, match, regex_test)){
AddTestNGLog((boost::format("%s:%s is dismatch with %s") % CANNYTHRESHOLDHL % m_strCannyThresholdSpec % reg).str());
return bRes;
}
boost::algorithm::split(vec_temp, m_strCannyThresholdSpec, boost::algorithm::is_any_of(","));
m_dCannyThresholdSpecH = static_cast<float>(atof(vec_temp[^0].c_str()));
m_dCannyThresholdSpecL = static_cast<float>(atof(vec_temp[^1].c_str()));

this->HelperGetProp(MINLINELENGTHRATIO, m_dMinLineLengthRatio);
m_vecLetterChartUI.emplace_back(std::make_pair(MINLINELENGTHRATIO, to_string(m_dMinLineLengthRatio)));

if (!CheckSpecRationality(ANGLESPEC, m_dAngleSpec, 5)) return bRes;
if (!CheckSpecRationality(WIDTHSPEC, m_dWidthSpec, 5)) return bRes;
if (!CheckSpecRationality(XYDIFFSPEC, m_dXYDiffSpec, 160)) return bRes;

m_vecLetterChartUI.insert(m_vecLetterChartUI.end(), ChartCheckBase::m_vecUI.begin(), ChartCheckBase::m_vecUI.end());
//TRUE;
//FALSE;
this->HelperGetProp(DEBUGMODE, m_bDebugMode);
m_vecLetterChartUI.emplace_back(std::make_pair(DEBUGMODE, (m_bDebugMode ? "TRUE" : "FALSE")));

bRes = true;
return bRes;
}

/**
* @brief 动态加载字母图表检测DLL库
*
* 该函数负责加载字母图表检测功能所需的动态链接库，主要完成以下功能：
* 1. 根据当前程序路径构造DLL文件的完整路径
* 2. 根据编译模式（Debug/Release）选择对应的DLL文件
* 3. 检查DLL文件是否存在
* 4. 动态加载DLL库到内存中
* 5. 获取DLL中的测试函数地址
*
* @return bool - 库加载结果
* true: DLL库加载成功且函数地址获取成功
* false: DLL文件不存在或加载失败或函数地址获取失败
*
* @note 函数执行流程：
* 1. 从全局程序路径PROGRAM_PATH中提取目录路径
* 2. 根据编译模式拼接完整的DLL文件路径
* 3. 使用PathFileExists检查DLL文件是否存在
* 4. 使用LoadLibraryEx加载DLL库
* 5. 使用GetProcAddress获取LetterChartCheckCale函数地址
* 6. 任何一步失败都会记录错误日志并返回false
*
* @details 路径处理规则：
* - Debug模式加载LetterChartCheckd.dll
* - Release模式加载LetterChartCheck.dll
* - 使用LOAD_WITH_ALTERED_SEARCH_PATH标志加载DLL
* - 所有错误情况都会通过AddTestNGLog记录详细错误信息
*/
bool Leo_LetterChart::LoadLibraryDLL()
{
//加载dll
bool res = false;


size_t i = PROGRAM_PATH.rfind("\\");
string m_strFilePath = PROGRAM_PATH.substr(0, i + 1);

#ifdef _DEBUG
m_strFilePath += "LetterChart\\LetterChartCheckd.dll";

#else
m_strFilePath += "LetterChart\\LetterChartCheck.dll";

#endif // _DEBUG

CString m_CStrFilePath = m_strFilePath.c_str();
if (!PathFileExists(m_CStrFilePath))
{
AddTestNGLog((boost::format("Can't find .dll file path:%s") % m_strFilePath).str());
return res;
}
m_hLetterChartCheckDll = LoadLibraryEx(m_CStrFilePath, NULL, LOAD_WITH_ALTERED_SEARCH_PATH);
if (NULL == m_hLetterChartCheckDll)
{
AddTestNGLog((boost::format("Can't Find file:%s") % m_strFilePath).str());
return res;
}
m_LetterChartTestProg = (LetterChartCheckCale)GetProcAddress(m_hLetterChartCheckDll, "LetterChartCheckCale");
if (NULL == m_LetterChartTestProg)
{
AddTestNGLog((boost::format("Can't Find LetterChartCheckCale Function")).str());
FreeLibrary(m_hLetterChartCheckDll);
return res;
}

res = true;
return res;
}


/**
* @brief 释放动态加载的字母图表检测DLL库
*
* 该函数负责安全地释放之前通过LoadLibraryDLL函数加载的字母图表检测动态链接库，
* 释放系统资源并确保程序正确清理。主要用于在测试完成或对象销毁时进行资源回收。
*
* @return bool - 库释放结果
* true: DLL库释放成功（总是返回true）
*
* @note 函数执行流程：
* 1. 使用SAFE_FREE_LIBRARY宏安全释放DLL库句柄
* 2. 将库句柄置为无效状态
* 3. 返回释放成功状态
*
* @details 安全释放机制：
* - 使用SAFE_FREE_LIBRARY宏确保释放操作的线程安全性
* - 宏内部会检查句柄有效性，避免重复释放或空指针问题
* - 释放后m_hLetterChartCheckDll句柄将被置为NULL
* - 该函数通常与LoadLibraryDLL配对使用，确保资源管理的一致性
*/
bool Leo_LetterChart::FreeLibraryDLL()
{
bool bRes = false;
SAFE_FREE_LIBRARY(m_hLetterChartCheckDll);
bRes = true;
return bRes;

}

/**
* @brief 执行字母图表测试流程
*
* 该函数是字母图表测试的核心执行函数，主要完成以下功能：
* 1. 将输入的Qt图像转换为OpenCV Mat格式
* 2. 将彩色图像转换为灰度图像
* 3. 设置测试参数并调用测试程序
* 4. 根据测试结果进行相应处理
* 5. 对角度、宽度、X/Y方向偏移等测试项进行规格判定
* 6. 记录测试结果到输出向量和日志系统
*
* @return bool - 测试结果
* true: 所有测试项均通过规格要求
* false: 至少有一个测试项未通过规格要求或测试过程中出现错误
*
* @note 函数执行流程：
* 1. 图像格式转换失败时立即返回false
* 2. 调用m_LetterChartTestProg进行核心测试
* 3. 根据返回码处理不同错误情况
* 4. 对四个测试项（角度、宽度、X偏移、Y偏移）分别进行规格判定
* 5. 将所有测试结果记录到m_vecLetterChartOutputPDF向量中
*
* @details 测试项规格判定规则：
* - 测试值必须在开区间(lower, upper)内才算通过
* - 任一测试项超出规格范围都会导致最终结果为false
* - 每个测试项的结果都会单独记录到日志系统
*/
bool Leo_LetterChart::Run()
{
bool bRes = false;

if (!QtImageToCvMat(m_pTestItem->in_image, m_Image))
{
AddTestNGLog("QtImageToCvMat failed!");
return bRes;
}

//cv::Mat grayImage;
//cv::cvtColor(m_Image, grayImage, cv::COLOR_BGR2GRAY);
cv::Mat grayImage;

if (m_Image.channels() != 3)
{
AddTestNGLog((boost::format(" input Image channels error:%d") % m_Image.channels()).str());
return bRes;
}

try
{
cv::cvtColor(m_Image, grayImage, cv::COLOR_BGR2GRAY);
}
catch (const cv::Exception& e)
{
AddTestNGLog((boost::format("OpenCV error: %s") % e.what()).str());
return bRes;
}

m_inputInfo.m_pImage = grayImage.data;
m_inputInfo.m_iWidth = in_image.width;
m_inputInfo.m_iHeight = in_image.height;
m_inputInfo.m_dCannyThresholdH = m_dCannyThresholdSpecH;
m_inputInfo.m_dCannyThresholdL = m_dCannyThresholdSpecL;
m_inputInfo.m_dMaxLineCap = MaxLineCapNum;
m_inputInfo.m_dMinLineRatio = m_dMinLineLengthRatio;
m_inputInfo.m_bDebugMode = m_bDebugMode;

int iRes = m_LetterChartTestProg(m_inputInfo, m_outInfo);
switch (iRes)
{
case 0:
bRes = true;
AddTestLog("Succ!");
break;
case 1:
AddTestNGLog("Input parameter error");
break;
case 2:
AddTestNGLog("Insufficient number of image line segments in the V direction");
break;
case 3:
AddTestNGLog("Insufficient number of image line segments in the H direction");
break;
case 4:
AddTestNGLog("No matching segments found");
break;
case 5:
AddTestNGLog("Intersection calculation failed");
break;

default:
AddTestNGLog("Unknown errors");
break;
}

if (!bRes)
return bRes;


vector<double>value, lower, upper;
vector<string>itemName{ "Angle", "Width", "XDiff", "YDiff" };

value.push_back(m_outInfo.m_dMeanAngle);
value.push_back(m_outInfo.m_dMeanDistance);

double x_diff = m_inputInfo.m_iWidth / 2 - m_outInfo.m_dCrossPoint[^0];
double y_diff = m_inputInfo.m_iHeight / 2 - m_outInfo.m_dCrossPoint[^1];

value.push_back(x_diff);
value.push_back(y_diff);

lower.push_back(m_dAngleSpec.first);
lower.push_back(m_dWidthSpec.first);
lower.push_back(m_dXYDiffSpec.first);
lower.push_back(m_dXYDiffSpec.first);

upper.push_back(m_dAngleSpec.second);
upper.push_back(m_dWidthSpec.second);
upper.push_back(m_dXYDiffSpec.second);
upper.push_back(m_dXYDiffSpec.second);


for (size_t i = 0; i < itemName.size(); ++i)
{
double currentValue = value[i];
double currentLower = lower[i];
double currentUpper = upper[i];

string str_currentValue = (boost::format("%.6lf") % currentValue).str();
string str_currentLower = (boost::format("%.6lf") % currentLower).str();
string str_currentUpper = (boost::format("%.6lf") % currentUpper).str();

std::string currentItemName = itemName[i];
if (currentValue <= currentLower || currentValue >= currentUpper)
{
m_vecLetterChartOutputPDF.emplace_back(std::make_tuple("Fail", currentItemName,
				str_currentLower, str_currentValue, str_currentUpper, ENUM_THRESHOLD_TYPE::LOWER_OPEN_UPPER_OPEN));
AddTestNGLog("OVER RANGE");
AddTestNGLog((boost::format(" the testing item: %s, test value:%.6f, Spec(%.6f,%.6f)") % currentItemName % currentValue % currentLower % currentUpper).str());
bRes &= false;
}
else
{
// 添加通过记录到输出向量
m_vecLetterChartOutputPDF.emplace_back(std::make_tuple("Pass", currentItemName,
				str_currentLower, str_currentValue, str_currentUpper, ENUM_THRESHOLD_TYPE::LOWER_OPEN_UPPER_OPEN));
AddTestOKLog((boost::format(" the testing item: %s, test value:%.6f, Spec(%.6f,%.6f)") % currentItemName % currentValue % currentLower % currentUpper).str());
}
}
return bRes;
}

/**
* @brief 输出测试数据到PDF报告
*
* 将LetterChart测试的UI参数配置和测试结果数据输出到PDF报告中。
* 包括界面参数配置表格和带上下限规格的测试结果表格，同时输出相关图像。
*
* @return bool 返回PDF输出操作结果
* - true: PDF输出成功完成
* - false: PDF输出过程中发生异常
*
* @note 函数执行流程：
* 1. 写入测试项名称标题
* 2. 输出UI参数配置表格（名称-值格式）
* 3. 输出测试结果表格（名称-值-规格下限-规格上限格式）
* 4. 输出相关图像到PDF
* 5. 清理图像列表
* 6. 记录操作日志
*
* @see m_vecLetterChartUI UI参数向量，存储界面配置参数
* @see m_vecLetterChartOutputPDF 测试结果向量，存储测试结果和规格信息
* @see m_img_list 图像列表，存储需要输出到PDF的图像
* @see in_out_db->GetReport().WriteTable() 报表表格写入函数
* @see in_out_db->GetReport().WriteImage() 报表图像写入函数
*/
bool Leo_LetterChart::OutPutPDF()
{
bool bqt_res = false;
std::list< std::pair< std::list<std::string>, unsigned int > > write_data;
try
{
m_pTestItem->in_out_db->GetReport().WriteCaption(m_pTestItem->GetName());
//界面
if (!m_vecLetterChartUI.empty())
{
write_data.push_back(std::make_pair((std::list<std::string>)boost::assign::list_of("Name")("Value"), 0));
for (const auto& ui : m_vecLetterChartUI)
{
				write_data.push_back(std::make_pair((std::list<std::string>)boost::assign::list_of(ui.first)(ui.second), 0));
}
in_out_db->GetReport().WriteTable(write_data);
write_data.clear();
}
//带上下限的数据
if (!m_vecLetterChartOutputPDF.empty())
{
write_data.push_back(std::make_pair((std::list<std::string>)boost::assign::list_of("Name")("Value")("SpecL")("SpecH"), 0));
for (const auto& res : m_vecLetterChartOutputPDF)
{
				write_data.push_back(std::make_pair((std::list<std::string>)boost::assign::list_of(std::get<1>(res))(std::get<3>(res))(std::get<2>(res))(std::get<4>(res)), 0));
}
in_out_db->GetReport().WriteTable(write_data);
write_data.clear();
}
in_out_db->GetReport().WriteImage(m_img_list);
m_img_list.clear();
AddTestLog("Write data to PDF Successfully");
}
catch (const exception& ex)
{
AddTestNGLog((boost::format("Error:Write data to PDF error, and the error message is: %s") % ex.what()));
return bqt_res;
}

bqt_res = true;
return bqt_res;
}

/**
* @brief 上传测试数据到数据库和MES系统
*
* 将LetterChart测试结果数据上传到数据库和MES（制造执行系统）中，
* 使用SQL操作宏进行数据插入，支持数值型数据的转换和处理。
*
* @return bool 返回数据上传操作结果
* - true: 数据上传成功
* - false: 数据上传失败
*
* @note 函数执行流程：
* 1. 获取模块名称和产品序列号
* 2. 开始SQL事务操作
* 3. 遍历测试结果向量，将有效数据插入数据库和MES
* 4. 处理"NAN"特殊值的转换（转换为SHRT_MIN）
* 5. 提交事务并记录操作结果
* 6. 输出操作日志信息
*
* @see m_vecLetterChartOutputPDF 测试结果向量，包含测试数据
* @see SQL_ADD_BEGIIN SQL事务开始宏
* @see SQL_ADDMES MES数据插入宏
* @see SQL_ADD_END SQL事务结束宏
* @note 使用atof进行字符串到浮点数的转换，处理数值型测试数据
*/
bool Leo_LetterChart::OutPutMes()
{
AddTestOKLog("Insert Data to Mes and Database - Start");
string m_strModuleName = m_pTestItem->GetName();
string m_strSn = m_pTestItem->in_out_hw->GetMoreProp(SN);
bool bResult = false;



string Log = "";
SQL_ADD_BEGIIN(m_strModuleName, qtUtility_OutDataContainer, in_out_db, m_bResult, Log, bResult, m_strSn);

for (const auto& res : m_vecLetterChartOutputPDF)
{
if (std::get<3>(res) != "NAN")
{
SQL_ADDMES_STR("", std::get<0>(res), ((std::get<4>(res) == "NAN") ? to_string(SHRT_MIN) : std::get<4>(res)), std::get<3>(res), ((std::get<2>(res) == "NAN") ? to_string(SHRT_MIN) : std::get<2>(res)), std::get<1>(res), "", std::get<5>(res));
// 			SQL_ADDMES("", std::get<0>(res), ((std::get<4>(res) == "NAN") ? SHRT_MIN : (float)atof(std::get<4>(res).c_str())), (float)atof(std::get<3>(res).c_str()), ((std::get<2>(res) == "NAN") ? SHRT_MIN : (float)atof(std::get<2>(res).c_str())), std::get<1>(res), "", std::get<5>(res));
}
}

SQL_ADD_END;
if (!bResult)
{
AddTestNGLog(Log);
return false;
}
AddTestOKLog("Insert Data to Mes and Database success");
return bResult;
}

---
## 🤖 Assistant | 深度求索

根据C++代码审查协议，我对提供的Leo_LetterChart类代码进行专业评估：

## 代码审查报告

### 审查范围
- Leo_LetterChart.h - 类定义和接口声明
- Leo_LetterChart.cpp - 类实现细节
- 重点关注：图像处理、DLL加载、资源管理、错误处理

### 主要发现

#### 1. 内存管理问题
**严重程度：高**
- `Run()`函数中`grayImage.data`直接赋值给`m_inputInfo.m_pImage`存在风险
```cpp
m_inputInfo.m_pImage = grayImage.data;  // 危险：grayImage超出作用域后数据无效
```
**建议**：需要确保图像数据在DLL调用期间保持有效

#### 2. 类型安全缺陷
**严重程度：中**
- 函数指针转换缺少类型检查
```cpp
m_LetterChartTestProg = (LetterChartCheckCale)GetProcAddress(...);
```
**建议**：添加函数签名验证，使用`reinterpret_cast`明确转换意图

#### 3. 错误处理不完整
**严重程度：中**
- `GetUIParam()`中正则表达式匹配失败后直接返回，未清理部分初始化的成员变量
- `Run()`函数中异常捕获后仅记录日志，未提供恢复机制

#### 4. 资源泄露风险
**严重程度：中**
- 构造函数/析构函数为空，但涉及DLL句柄管理
- 缺少拷贝控制（拷贝构造函数、赋值操作符应禁用）

#### 5. 性能问题
**严重程度：低**
- `OutPutPDF()`和`OutPutMes()`中大量字符串格式化操作
- 图像转换可能产生不必要的数据拷贝

### 具体问题分析

#### 图像处理模块
```cpp
// 问题：图像通道检查逻辑可能过于严格
if (m_Image.channels() != 3) {
    AddTestNGLog((boost::format(" input Image channels error:%d") % m_Image.channels()).str());
    return bRes;
}
```
**建议**：支持单通道灰度图像输入，或提供明确文档说明

#### DLL加载模块
```cpp
// 问题：路径构造依赖外部宏，缺乏灵活性
#ifdef _DEBUG
    m_strFilePath += "LetterChart\\LetterChartCheckd.dll";
#else
    m_strFilePath += "LetterChart\\LetterChartCheck.dll";
#endif
```
**建议**：提供配置化路径设置，增强部署灵活性

### 安全漏洞
- 未对DLL文件进行数字签名验证
- 用户输入的规格参数缺少边界检查
- SQL操作中直接使用字符串拼接，存在注入风险

### 代码质量改进建议

1. **RAII应用**：使用智能指针管理DLL句柄
2. **异常安全**：确保资源在异常情况下正确释放
3. **输入验证**：对所有用户输入参数进行严格验证
4. **日志改进**：区分不同级别的日志信息
5. **单元测试**：为关键算法添加测试用例

### 总结
该代码在功能实现上基本完整，但在生产环境部署前需要重点解决内存管理、资源泄露和安全性问题。建议优先处理标记为"高"严重程度的问题，特别是图像数据生命周期管理和DLL加载安全机制。

**审查完成** - 所有发现基于提供的代码文件，未超出既定审查范围。
