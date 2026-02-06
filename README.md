# Recurring Incidents (Zabbix Dashboard Widget)

This repository contains a Zabbix frontend module that adds a **Recurring Incidents** dashboard widget. The widget helps continuous improvement teams identify recurring problems, track key reliability signals (e.g., MTTR/MTBF), and spot trends over time.
<img width="1188" height="624" alt="image" src="https://github.com/user-attachments/assets/8815d9f8-bcc6-4e7a-8891-814a48a72a7a" />
<img width="711" height="763" alt="image" src="https://github.com/user-attachments/assets/b65837c5-4ba6-4855-a702-d7f8080c5607" />


## Features

- **Recurring detection**: counts occurrences per trigger within the selected time period.
- **Accurate recurrence counts**: recurrence is computed using aggregated event counts (not affected by UI paging/search limits).
- **Trend**: compares current period vs previous period of the same duration.
- **MTTR / MTBF**: calculated from event recovery information within the selected period.
- **MTBF “time remaining” hint**: indicates time remaining to reach MTBF (or how much it is exceeded) in the row “Info”.
- **Show modes** (like the native *Problems* widget):
  - Recent problems
  - Problems
  - History
- **Timeline rendering**: optional timeline view when sorting by time.
- **SLI (auto-detected)**:
  - Detects the impacted **Service** by matching event tags against Service *problem tags*.
  - Detects the corresponding **SLA** for that service automatically.
  - Displays **SLI** in the table.
  - Clicking SLI shows a tooltip with the full Service tree/path and quick links.

## Compatibility

- Developed and tested on **Zabbix 7.0.15**.
- Other Zabbix versions may behave differently and you may encounter errors. If that happens, please check your Zabbix frontend/web server/PHP logs and open an **issue** with the details so we can keep improving and updating the module.

## Installation

Copy (or clone) this module into your Zabbix frontend modules directory:

```bash
sudo mkdir -p /usr/share/zabbix/modules
sudo cp -a RecurringIncidents /usr/share/zabbix/modules/RecurringIncidents
```

Then reload the Zabbix frontend (or restart your web server/PHP-FPM if needed).

## Usage

1. Open a dashboard in Zabbix.
2. Click **Edit dashboard**.
3. **Add widget** → select **Recurring Incidents**.
4. Configure the widget fields (see below).

## Widget configuration

- **Show**: Recent problems / Problems / History
- **Host groups / Hosts / Exclude host groups**: scope the dataset
- **Problem**: text filter
- **Severity**: severity filter
- **Problem tags**: tag filter
- **Show tags / Tag name / Tag display priority**: tag rendering options
- **Minimum occurrences**: minimum number of occurrences required to appear in the list
- **Time period**: the analysis window used for recurrence, trend, MTTR/MTBF and SLI
- **Sort entries by**: time, severity, name, host, or recurrences
- **Show timeline**: timeline rendering when sorting by time
- **Show lines**: number of rows to display

## How recurrence is computed

Recurrence is calculated as the **count of trigger problem events** within the selected **Time period**, grouped by trigger.

The widget uses aggregated counts to avoid undercounting due to frontend paging or search limits.

## How Trend is computed

Trend compares the **current Time period** against the **previous period of the same duration**:

- `trend = occurrences(current_period) - occurrences(previous_period)`

Positive values indicate increased recurrence; negative values indicate improvement.

## How MTTR / MTBF are computed

- **MTTR**: average of `(recovery_time - problem_time)` for events that have a recovery event within the selected period.
- **MTBF**: average time between consecutive occurrences within the selected period.

If there are no resolved occurrences in the selected period, MTTR is shown as `—`.

## How SLI is auto-detected

If your Zabbix Services/SLA are configured:

1. The widget matches the event **tags** against Service **problem tags** to find a corresponding Service.
2. It discovers the SLA associated with that Service.
3. It queries SLA SLI for the selected Time period and displays it.

Clicking the **SLI** cell opens a tooltip showing:

- Service name and link to the Service tree page
- SLA/SLO
- Service path (root → … → service)
- Service tree (limited for performance if very large)

## Performance notes

- Large environments with many triggers/events/services may require tuning Zabbix frontend limits and PHP resources.
- Service tree tooltips limit the number of loaded nodes to prevent heavy API calls.

## Development notes

Main module files:

- `manifest.json`
- `Widget.php`
- `actions/WidgetView.php`
- `includes/WidgetForm.php`
- `includes/WidgetRecurringIncidents.php`
- `views/widget.edit.php`
- `views/widget.view.php`
- `assets/js/class.widget.js`

## Credits

This module was developed by **Monzphere**.

Special thanks to our partners: **Lunio**, **IOS**.

Website: `https://monzphere.com`

## License

This module is intended to be used with Zabbix. Ensure your distribution and modifications comply with the licensing terms applicable to your Zabbix frontend installation and this repository.

