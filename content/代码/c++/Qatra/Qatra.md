---
title: "Qt视图桥接与图像显示模块"
description: "包含Qt视图桥接的图像显示功能、时间戳文件保存、字符串格式验证和规格参数解析的C++代码实现。"
permalink: "/qt-view-bridge-image-display"
tags: [Qt视图桥接, 图像显示, 格式验证, 参数解析, C++开发]
aliases: [Qt视图桥接, 图像显示, 格式验证, 参数解析, C++开发]
created: "2025-01-15"
modified: "2025-01-15"
published: "2025-01-15"
publish: false
draft: false
enableToc: true
lang: "zh-CN"
---

## `//向主框架显示图片`

![[Pasted image 20230419102308.png]]

`	qtView_Bridge::getInst(m_pTestItem->m_id)->DisplayImage(in_out_hw->GetDisplayOrder() + in_out_hw->GetObjectId(), m_disImg);

`


SYSTEMTIME st;
	::GetLocalTime(&st);//当前时间
	std::string strTime = str(boost::format("%d_%02d_%02d_%02d_%02d_%02d_%03d") % st.wYear % st.wMonth % st.wDay % st.wHour % st.wMinute % st.wSecond % st.wMilliseconds);
	string strDirPath = CStrToChar(GetCurrentPath()) + (string)("\\") + strTime + (string)(".png");
	AddTestNGLog(strDirPath);
	cv::imwrite(strDirPath, m_disImg);
	//向主框架显示图片
	qtView_Bridge::getInst(m_pTestItem->m_id)->DisplayImage(in_out_hw->GetDisplayOrder() + in_out_hw->GetObjectId(), m_disImg);

## 格式控制


	//====================================CenterField================================
	this->HelperGetProp(SPEC_ROI_FIELDCT, strTemp);
	boost::remove_erase_if(strTemp, boost::is_any_of("[\\/:*?""<>|] "));
	flag = CheckStrFormat("^(\\d(\\.\\d)?)+,(\\d(\\.\\d)?)+$", strTemp);
	if (!flag)
	{
		in_param << "格式错误" << ("\n") << "CenterFieldSpec格式错误，请检查配置项格式是否正确！" << ("\n");
		in_controller->BlockingShowModalessDialog("qtUtilityGui_SimpleMain", in_param.str(), rtn);
		AddNGLog("CenterField格式错误，请检查配置项格式是否正确");
		return false;
	}
	ret = sscanf(strTemp.c_str(), "%f,%f", &m_SFRTestPara.st_specpass[0], &m_SFRTestPara.st_specpass[1]);
	if (ret != 2)
	{
		in_param << "解析错误" << ("\n") << "Get Spec of Center Error 中文：中心区域规格解析错误，请检查标点符号！" << ("\n");
		in_controller->BlockingShowModalessDialog("qtUtilityGui_SimpleMain", in_param.str(), rtn);
		AddNGLog("Get Spec of Center Error");
		return false;
	}
	else
	{	
		SpecCT.Val_CT_V = m_SFRTestPara.st_specpass[1];
	}

bool qtmALGO_DajiangPreSFR::CheckStrFormat(std::string strFormat, std::string strCheck)
{
	std::regex base_regex(strFormat);//正则表达式
	std::smatch base_match;//存放匹配的字段
	return std::regex_match(strCheck, base_match, base_regex);
}
[[MES上传验证]]


```cpp
		template <typename _Type>
		bool splitLH(const string offsetstr, _Type *p)
		{
			vector<string> vec_offset;
			vec_offset.clear();
			boost::split(vec_offset, offsetstr, boost::is_any_of(","), boost::token_compress_on);
			if ((vec_offset[1] < vec_offset[0]) || vec_offset.size() != 2)
			{
				AddTestNGLog("The Upper and Lower Limits On UI Error");
				return false;
			}
			return true;
		}

```

```cpp
bool ANSFR_Base::splitLH(string &sfr, float* p)
{
	std::string::size_type pos;
	pos = sfr.find(",");
	if (pos != std::string::npos)
	{
		std::string sf1 = sfr.substr(0, pos);
		std::string sf2 = sfr.substr(pos + 1, sfr.size());
		*(p + 0) = stof(sf1);
		*(p + 1) = stof(sf2);
		if (!(*(p + 0) < *(p + 1)))
			AddTestNGLog("The Upper and Lower Limits On UI Error");
		return *(p + 0) < *(p + 1) ? true : false;

	}
	else
		return false;
}

```