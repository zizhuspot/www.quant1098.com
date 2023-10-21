---
title: Timing Strategy Backtesting Visualization Panel
date: 2023-10-15 16:07:00
categories:
  - quantitative trading
tags:
  - Digital currency
  - Stock
  - Quantitative tool
  - Visualization Panel
  - Backtesting
  - quant1098.com
  - Strategy
description: This visualization panel is applicable to the visualization of the backtest results of a series of time-testing strategies with only different parameters and all other backtesting conditions are the same, which integrates the post based on the visualization of the backtest results of the n-parameter strategy with parallel coordinates, a better evaluation function of the capital curve, and the results of the strategy visualization echarts charts enhancement (new version), which contains quite a comprehensive visualization and analysis functions.
cover: https://s2.loli.net/2023/10/21/X9jC3sc1OBqHLmK.gif
---

This visualization panel is applicable to the visualization of the `backtest results` of a series of time-testing strategies with only different parameters and all other `backtesting` conditions are the same, which integrates the post based on the visualization of the `backtest results` of the n-parameter strategy with parallel coordinates, a better evaluation function of the capital curve, and the results of the strategy visualization `echarts` charts enhancement (new version), which contains quite a comprehensive visualization and analysis functions.

## Function

- The drop-down box in the upper left corner allows you to select the strategy parameters. You can view the overall `backtesting` results for all parameters before selecting them, and you can additionally view the `backtesting results` corresponding to the parameter after selecting it.
- The navigation bar on the left is used to switch between different functions. The pages with the suffix "-chart" show charts, which are described in detail in the Parallel coordinates-based visualization of n-parameter strategy `backtesting results` and the strategy visualization `echarts` chart enhancement (new version). The page with "-table" suffix shows the underlying data of the corresponding chart, and has filtering, sorting and other functions.

## Effect display

The diagrams of each function are as follows (in order from top to bottom):

![](https://s2.loli.net/2023/10/21/1s7AWGpJXKm5agk.webp)

![](https://s2.loli.net/2023/10/21/9zxIWNfL6ljTQcG.webp)

![](https://s2.loli.net/2023/10/21/pJ4fqDvVuwbnBQ8.webp)

![](https://s2.loli.net/2023/10/21/ebqMxf3UjyLXDvV.webp)

![](https://s2.loli.net/2023/10/21/JhaZWtpXo6kHuPb.webp)

## How to use

1. Install the dash,dash_bootstrap_components,dash_extensions libraries.

2. Download and extract the code files to a separate folder.

3. Modify the global variables in the configuration file config.py and the contents of the five functions, be careful not to modify the number of parameters of the function, if you need other information can add global variables. For specific modification requirements, please refer to the comments in the file and the following instructions:

   - The return value of `get_param_name` should be shaped like ['n', 'm'], which is used for interface display and charting.

   - The return value of `get_param_list` should look like [[200, 2.0], [200, 3.0], [300, 2.0], [300, 3.0]], which is used to provide the options in the drop-down box.

   - The return value of `get_all_data_df` should be a `DataFrame`, for which the requirements are consistent with those in the post Parallel coordinates-based visualization of `backtesting` results for n-parameter strategies.

   - The return value of `get_param_data_df` should be a `DataFrame`, and the requirements for it are consistent with those in the post Strategy Visualization `echarts` Chart Enhancements (new version).

   - The return value of get_plot_info should be `PlotInfo`, the requirements for which are the same as in the post strategy visualization `echarts` chart enhancement (new version).

4. Note the source of the following functions, if they do not exist, you can import or copy and paste them into the corresponding file:

   - from plot import ... The source of the functions in the new version of `echarts` Charting for Strategic Visualization is in the Appendix.

   - from statistic import ... The source of the function in is the better funding curve evaluation function.

5. Run app.py and visit http://127.0.0.1:8050/ with your browser.

## Note

The file_system_store folder that is automatically generated in the code folder is used to cache data and will be automatically deleted after the program exits.

## appendice

```python
def get_kline_df(df, plot_info):
    """
    生成kline_df：开高低收+成交量+指标的DataFrame

    :param pd.DataFrame df:
    :param PlotInfo plot_info:
    :return:
    :rtype: pd.DataFrame
    """

    kline_df = df[['candle_begin_time', 'open', 'high', 'low', 'close', 'volume'] + plot_info.to_list(name_only=True, flattened=True)]

    return kline_df


def get_curve_df(df):
    """
    生成curve_df：价格曲线+资金曲线+回撤曲线的DataFrame

    :param pd.DataFrame df:
    :return:
    :rtype: pd.DataFrame
    """

    df['price_curve'] = df['close'] / df.iloc[0]['close']
    df['max_equity_curve'] = df['equity_curve'].expanding().max()
    df['drawdown'] = 1 - df['equity_curve'] / df['max_equity_curve']
    curve_df = df[['candle_begin_time', 'price_curve', 'equity_curve', 'max_equity_curve', 'drawdown']]

    return curve_df


def get_trade_df(trade):
    """
    生成trade_df：逐笔交易的DataFrame

    :param pd.DataFrame trade:
    :return:
    :rtype: pd.DataFrame
    """

    trade_ratio_name_list = ['end_ratio', 'min_ratio', 'max_ratio']
    trade_time_name_list = ['begin_time', 'end_time', 'hold_time', 'signal'] + (['leverage_rate'] if 'leverage_rate' in trade.columns else [])
    trade_column_name_list = trade_ratio_name_list + trade_time_name_list
    if not trade.empty:
        trade_df = trade[trade_column_name_list].copy()
        trade_df[trade_ratio_name_list] = trade_df[trade_ratio_name_list].fillna(value=0)
        if set(trade_df['signal'].unique()) <= {0, 1, -1}:
            trade_df['signal'] = trade_df['signal'].astype(int)
        trade_df[trade_time_name_list] = trade_df[trade_time_name_list].astype(str)
        trade_df.reset_index(drop=False, inplace=True)
        trade_df['index'] += 1
    else:
        trade_df = pd.DataFrame(columns=trade_column_name_list)

    return trade_df


def get_signal_df(df):
    """
    生成signal_df：开平仓标记点的DataFrame

    :param pd.DataFrame df:
    :return:
    :rtype: pd.DataFrame
    """

    if 'pos' in df.columns:
        signal_df = df[df['pos'] != df['pos'].shift(1, fill_value=0)].copy()
        signal_df['equity_change'] = signal_df['equity_change'].shift(-1)
        signal_df.reset_index(drop=False, inplace=True)
        signal_df = signal_df[['index', 'pos', 'equity_change']]
    else:
        signal_df = pd.DataFrame()

    return signal_df


def merge_df_trade(df, trade):
    if not trade.empty:
        trade['equity_change'] = trade['end_ratio']
        df.drop(['equity_change'], axis=1, inplace=True)
        df = df.merge(trade[['end_time', 'equity_change']], how='left', left_on='candle_begin_time', right_on='end_time')
        df.drop(['end_time'], axis=1, inplace=True)
        trade.drop(['equity_change'], axis=1, inplace=True)
    else:
        df.drop(['pos', 'equity_change'], axis=1, inplace=True)
    return df


def get_precision(number):
    atol = 1e-10
    rtol = 1e-6
    tol = min(atol, rtol * abs(number))
    precision = 0
    while abs(round(number, precision) - number) > tol:
        precision += 1
    return precision


def get_columns_precision(df, columns):
    """
    获取df的columns中所有小数的最大精度

    :param pd.DataFrame df:
    :param Any columns:
    :return:
    :rtype: int
    """

    return df[columns].applymap(get_precision).max().max()

```

## file structure

![2023-10-21_082119.png](https://s2.loli.net/2023/10/21/8yxgE3Z65HpXovn.png)

`app.py`

```python
# -*- coding: utf-8 -*-
from ast import literal_eval

import dash
from dash import html, dcc, Output, Input, State
from dash.exceptions import PreventUpdate
import dash_bootstrap_components as dbc
from dash_extensions.enrich import DashProxy, MultiplexerTransform, ServersideOutputTransform, ServersideOutput
import pandas as pd

from plot import get_precision, get_columns_precision, merge_df_trade, get_kline_df, get_curve_df, get_trade_df, get_signal_df
from statistic import strategy_evaluate, fmt

from config import get_param_name, get_param_list, get_all_data_df, get_param_data_df, get_plot_info, basic_config, echarts_config
from utility import filter_columns
from page import register_page

app = DashProxy(
    __name__,
    pages_folder='',
    use_pages=True,
    external_scripts=['https://cdn.staticfile.org/echarts/5.4.0/echarts.min.js'],
    external_stylesheets=['https://cdn.staticfile.org/bootstrap/5.2.3/css/bootstrap.min.css'],
    suppress_callback_exceptions=True,
    transforms=[
        MultiplexerTransform(),
        ServersideOutputTransform(),
    ],
)

register_page(app)

path_name_list = [*dict.fromkeys([page['module'].split('.')[0] for page in dash.page_registry.values()])]
param_path_name_list = [path for path in path_name_list if path not in ['basic', 'param']]

sidebar = html.Div(
    [
        html.H2('策略回测面板', className='display-6', style={'wordBreak': 'keep-all'}),
        html.Hr(),
        html.P(f'参数：{get_param_name()}', className='lead'),
        dcc.Dropdown(list(map(lambda x: str(type(x)(map(lambda y: round(y, get_precision(y)), x))), get_param_list())), id='param'),
        html.Hr(),
        dbc.Nav([dbc.NavLink(page['name'], href=page['path'], active='exact') for page in dash.page_registry.values()], vertical=True, pills=True),
        dbc.Toast('请先选择参数', header='提示', id='toast', is_open=False, dismissable=True, duration=2000, icon='info', className='position-fixed top-0 end-0'),
        *[dcc.Loading(dcc.Store(id=f'{path}-data'), fullscreen=True) for path in path_name_list],
        *[dcc.Loading(dcc.Store(id=f'{path}-echarts-data', data={}), fullscreen=True) for path in path_name_list if path not in ['basic', 'stats']],
        dcc.Loading(dcc.Store(id='echarts-config', data=echarts_config), fullscreen=True),
        dcc.Store(id='echarts-state'),
    ],
    className='d-flex flex-column',
    style={
        'padding': '2rem 1rem',
        'backgroundColor': '#f8f9fa',
    },
)

content = html.Div(
    dash.page_container,
    className='d-flex flex-column',
    style={
        'width': '100%',
        'height': '100vh',
        'overflow': 'auto',
    },
)

app.layout = html.Div(
    [
        dcc.Location(id='url'),
        sidebar,
        content,
    ],
    className='d-flex flex-row',
)


@app.callback(ServersideOutput('basic-data', 'data'), Input('url', 'pathname'), State('basic-data', 'data'))
def update_basic_data(pathname, data):
    if pathname == '/' and not data:
        return {
            'basic_df': pd.DataFrame([basic_config]).T.applymap(str),
        }
    raise PreventUpdate


@app.callback(ServersideOutput('param-data', 'data'), Input('url', 'pathname'), State('param-data', 'data'), State('echarts-config', 'data'))
def update_all_data(pathname, data, config):
    if pathname.startswith('/param') and not data:
        param_df = get_all_data_df()
        name_list = param_df.columns.to_list()
        param_name = get_param_name()
        return {
            'param_df': param_df,
            'name_list': name_list,
            'precision_list': [get_columns_precision(param_df, [name]) if name in param_name else config['table_precision'] for name in name_list],
        }
    raise PreventUpdate


@app.callback(
    *[ServersideOutput(f'{path}-data', 'data') for path in param_path_name_list],
    Output('echarts-config', 'data'), Input('param', 'value'), memoize=True,
)
def update_param_data(param_str):
    if param_str:
        param = literal_eval(param_str)
        df = get_param_data_df(param)
        trade, monthly_return, drawdown, result = strategy_evaluate(df.copy(), format_drawdown=False)
        df = merge_df_trade(df, trade)

        df['candle_begin_time'] = df['candle_begin_time'].astype(str)

        plot_info = get_plot_info()
        kline_df = get_kline_df(df, plot_info)
        curve_df = get_curve_df(df)
        trade_df = get_trade_df(trade)
        signal_df = get_signal_df(df)

        price_precision = get_columns_precision(kline_df, ['open', 'high', 'low', 'close'])  # 价格精度
        volume_precision = get_columns_precision(kline_df, ['volume'])  # 成交量精度

        return {
            'result': result.pipe(fmt).reset_index().rename(columns={'index': 'name'}).applymap(str),
            'monthly_return': monthly_return.reset_index().rename(columns={'candle_begin_time': 'time'}).pipe(lambda df: df.assign(**{'time': df['time'].dt.date})),
            'drawdown': drawdown.reset_index().pipe(lambda df: df.assign(**{column: df[column].map(str) for column in filter_columns(df, ('time', 'duration'))})),
            'trade': trade.reset_index().pipe(lambda df: df.assign(**{column: df[column].map(str) for column in filter_columns(df, 'time')})),
        }, {
            'kline_df': kline_df,
            'signal_df': signal_df,
            'plot_info': plot_info,
        }, {
            'curve_df': curve_df,
        }, {
            'trade_df': trade_df,
        }, {
            **echarts_config,
            'price_precision': price_precision,
            'volume_precision': volume_precision,
        }
    return {}, {}, {}, {}, echarts_config


@app.callback(Output('toast', 'is_open'), Input('url', 'pathname'), State('param', 'value'))
def open_toast(pathname, param_str):
    return pathname.removeprefix('/').startswith(tuple(param_path_name_list)) and not param_str


if __name__ == '__main__':
    from utility import register_clear_cache

    register_clear_cache()
    app.run_server()

```

`config.py`

```python
# -*- coding: utf-8 -*-


def get_variable_dict(_):
    return {key: value for key, value in globals().items() if key not in _ and key != '_'}


_ = globals().copy()
# 以下定义的所有变量会出现在基本信息当中

symbol = 'ETH-USDT'  # 币种
rule_type = '15T'  # K线时间周期
begin_date = '2017-10-01'  # 起始日期
end_date = '2022-06-30'  # 终止日期
signal_name = 'signal_simple_bolling'  # 策略函数名称

initial_cash = 10000  # 初始资金，默认为10000元
face_value = 0.01  # 一张合约的面值
slippage = 1 / 1000  # 滑点，可以用百分比，也可以用固定值。建议币圈用百分比，股票用固定值。
c_rate = 5 / 10000  # 手续费，commission fees，默认为万分之5。不同市场手续费的收取方法不同，对结果有影响。比如和股票就不一样。
min_margin_ratio = 1 / 100  # 最低保证金率，低于就会爆仓
leverage_rate = 3  # 杠杆倍数
drop_days = 0

# 以上定义的所有变量会出现在基本信息当中
basic_config = get_variable_dict(_)

_ = globals().copy()
# ----------以下参数可以修改，但在明白其效果之前不建议修改----------

# 以下高度比例的基准为去除图表顶部与底部后的视图高度
volume_height_ratio = 0.12  # 成交量图高度比例
subplot_height_ratio = 0.12  # 副图高度比例
kline_chart_interval_ratio = 0.06  # 副图垂直间隔高度比例
drawdown_height_ratio = 0.18  # 回撤图高度比例
curve_chart_interval_ratio = 0.09  # 净值图与回撤图垂直间隔高度比例
# 以下高度的单位为像素
top = 60  # 图表顶部保留的高度
bottom = 80  # 图表底部保留的高度

index_precision = 12  # 指标精度
curve_precision = 6  # 曲线精度
ratio_precision = 2  # 比例精度
table_precision = 2  # 回测结果与策略评价页面的数据精度

# ----------以上参数可以修改，但在明白其效果之前不建议修改----------
swap_color = True  # 是否交换K线颜色。True:绿涨红跌 False:红涨绿跌

echarts_config = get_variable_dict(_)


def get_param_name():
    """
    该函数用于获取策略参数名称的列表

    :return:
    :rtype: list
    """

    pass


def get_param_list():
    """
    该函数用于获取策略所有参数的列表

    :return:
    :rtype: list
    """

    pass


def get_all_data_df():
    """
    该函数用于获取策略所有参数的回测结果的DataFrame，可读取已有的结果或临时计算（后者可能速度较慢）

    :return:
    :rtype: pd.DataFrame
    """

    pass


def get_param_data_df(param):
    """
    该函数用于获取策略指定参数的回测结果的DataFrame，可读取已有的结果或临时计算（后者可能速度较慢）

    :param param: 参数
    :return:
    :rtype: pd.DataFrame
    """

    pass


def get_plot_info():
    """
    该函数用于获取策略的plot_info

    :return:
    :rtype: PlotInfo
    """

    pass

```

`page.py`

```python
# -*- coding: utf-8 -*-
from itertools import chain

import dash
from dash import html, dcc, Output, Input, State
from dash.exceptions import PreventUpdate
from dash_extensions.enrich import DashBlueprint
import dash_bootstrap_components as dbc

from utility import format_fixed, format_percentage, get_datatable_kwargs, table_from_dataframe, datatable_from_dataframe

page_list = ['基本信息', '回测结果-图', '回测结果-表', '策略评价', 'K线指标-图', 'K线指标-表', '资金曲线-图', '资金曲线-表', '逐笔交易-图', '逐笔交易-表']
page_kwargs_dict = {
    tuple(map(
        {
            '基本信息': 'basic',
            '回测结果': 'param',
            '策略评价': 'stats',
            'K线指标': 'kline',
            '资金曲线': 'curve',
            '逐笔交易': 'trade',
            '图': 'chart',
            '表': 'table',
        }.__getitem__, name.split('-')
    )): {'name': name, 'order': order} | ({'path': '/'} if order == 0 else {}) for order, name in enumerate(page_list)
}

echarts_data_function_dict = {
    'param': lambda data: {
        'param_dict': data['param_df'].to_dict('list'),
        'name_list': data['name_list'],
        'precision_list': data['precision_list'],
    },
    'kline': lambda data: {
        'kline_dict': data['kline_df'].to_dict('list'),
        'signal_list': data['signal_df'].to_numpy(dtype='object').tolist(),
        'plot_info_list': data['plot_info'].to_list(),
    },
    'curve': lambda data: {
        'curve_dict': data['curve_df'].to_dict('list'),
    },
    'trade': lambda data: {
        'trade_dict': data['trade_df'].to_dict('list'),
    },
}
datatable_kwargs_dict = {
    'param': lambda data, config: dict(zip(data['name_list'], map(format_fixed, data['precision_list']), strict=True)),
    'kline': lambda data, config: dict(chain.from_iterable(map(dict.items, map(
        dict.fromkeys,
        [['open', 'high', 'low', 'close'], ['volume'], data['plot_info'].to_list(name_only=True, flattened=True)],
        map(format_fixed, map(config.__getitem__, ['price_precision', 'volume_precision', 'index_precision']))
    )))),
    'curve': lambda data, config: get_datatable_kwargs(data['curve_df'], {('curve', 'drawdown'): format_fixed(config['curve_precision'])}),
    'trade': lambda data, config: get_datatable_kwargs(data['trade_df'], {'ratio': format_percentage(config['ratio_precision'])}),
}
other_function_dict = {
    'basic': lambda data, config: table_from_dataframe(data['basic_df'], header=['name', 'value'], index=True),
    'stats': lambda data, config: dbc.Tabs(
        [dbc.Tab(stats_function_dict[key](df, config), label=key, tab_style={'flex': 1}) for key, df in data.items()]
    ),
}
stats_function_dict = {
    'result': lambda df, config: table_from_dataframe(df),
    'monthly_return': lambda df, config: datatable_from_dataframe(df, **get_datatable_kwargs(df, {
        'change': format_percentage(config['table_precision']),
    })),
    'drawdown': lambda df, config: datatable_from_dataframe(df, **get_datatable_kwargs(df, {
        'equity': format_fixed(config['table_precision']),
        'drawdown': format_percentage(config['table_precision']),
    })),
    'trade': lambda df, config: datatable_from_dataframe(df, **get_datatable_kwargs(df, {
        'price': format_fixed(config['price_precision']),
        'equity': format_fixed(config['table_precision']),
        'ratio': format_percentage(config['table_precision']),
    })),
}


def make_page(page_name, page_type='', **page_kwargs):
    def p(cid):
        return f'{page_name}-{cid}'

    page = DashBlueprint()

    module_name = f'{page_name}.{page_type}'.removesuffix('.')

    if page_type == 'chart':

        page.layout = html.Div(id=p('echarts'), className='min-vh-100')

        @page.callback(Output(p('echarts-data'), 'data'), Input('url', 'pathname'), Input(p('data'), 'data'), State(p('echarts-data'), 'modified_timestamp'), State(p('data'), 'modified_timestamp'))
        def update_echarts_data(pathname, data, timestamp, data_timestamp):
            if pathname == dash.page_registry[module_name]['path']:
                if timestamp < data_timestamp:
                    if data:
                        return echarts_data_function_dict[page_name](data)
                    return {}
            raise PreventUpdate

        page.clientside_callback(
            dash.ClientsideFunction('clientside', f'init_{page_name}_echarts'),
            Output('echarts-state', 'data'), Input(dash.dash._ID_CONTENT, 'children'), Input(p('echarts-data'), 'data'), State('echarts-config', 'data'),
        )

    else:

        page.layout = dcc.Loading(id=p('content'), parent_className='min-vh-100')

        @page.callback(Output(p('content'), 'children'), Input('url', 'pathname'), Input(p('data'), 'data'), State('echarts-config', 'data'))
        def update_content(pathname, data, config):
            if pathname == dash.page_registry[module_name]['path']:
                if data:
                    return other_function_dict.get(
                        page_name,
                        lambda data, config: datatable_from_dataframe(data[f'{page_name}_df'], **datatable_kwargs_dict[page_name](data, config))
                    )(data, config)
                return None
            raise PreventUpdate

    return lambda app: page.register(app=app, module=module_name, **page_kwargs)


def register_page(app):
    for page_info, page_kwargs in page_kwargs_dict.items():
        make_page(*page_info, **page_kwargs)(app)

```

`utility.py`

```python
# -*- coding: utf-8 -*-
from itertools import chain

import dash
from dash.dash_table.Format import Format, Scheme
import dash_bootstrap_components as dbc


def format_fixed(precision):
    return {
        'type': 'numeric',
        'format': Format(precision=precision, scheme=Scheme.fixed),
    }


def format_percentage(precision):
    return {
        'type': 'numeric',
        'format': Format(precision=precision, scheme=Scheme.percentage),
    }


def filter_columns(df, suffix):
    return df.columns[df.columns.str.endswith(suffix)]


def get_datatable_kwargs(df, suffix_value_dict):
    return dict(chain.from_iterable(dict.fromkeys(filter_columns(df, suffix), value).items() for suffix, value in suffix_value_dict.items()))


def datatable_from_dataframe(df, **kwargs):
    return dash.dash_table.DataTable(
        data=df.to_dict('records'),
        columns=[dict.fromkeys(['name', 'id'], column) | kwargs.get(column, {}) for column in df.columns],
        cell_selectable=False,
        filter_action='native',
        sort_action='native',
        sort_mode='multi',
    )


def table_from_dataframe(df, **kwargs):
    return dbc.Table.from_dataframe(df, class_name='mx-auto w-50', **kwargs)


def register_clear_cache():
    import os
    import atexit

    # Disable Intel Fortran default console event handler
    env = 'FOR_DISABLE_CONSOLE_CTRL_HANDLER'
    if env not in os.environ:
        os.environ[env] = '1'

    @atexit.register
    def clear_cache():
        import shutil
        shutil.rmtree('file_system_store', ignore_errors=True)

```

