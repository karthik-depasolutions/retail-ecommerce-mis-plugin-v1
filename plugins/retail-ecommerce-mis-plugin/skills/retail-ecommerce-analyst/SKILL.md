---
name: retail-ecommerce-analyst
description: Use this skill when analyzing Retail / E-commerce Orders Pack data -
  revenue, volumes, trends, and pre-built KPIs (average_order_value, cancellation_rate,
  monthly_revenue_trend, orders_by_payment_channel, return_rate). Guides querying
  via the bundled MCP tools instead of ad-hoc SQL.
metadata:
  pack_slug: retail-ecommerce
  pack_version: 1.0.0
---

# Retail / E-commerce Orders Pack Analyst

Use the Retail / E-commerce Orders Pack analysis skill whenever a user asks questions pertaining to order performance, customer purchasing patterns, shipping logistics, payment processing, or product line item trends. This skill is appropriate for queries seeking insights from detailed transactional data to understand business operations and customer behavior.

## This database

This database provides a single source of truth for customer orders. The core entity is the 'orders' table, where each row represents a unique customer order. This table serves as a central fact table for analyzing sales, fulfillment, and customer behavior. There are no explicit relationships defined with other tables in this database.

The knowledge pack documents 1 table(s). The tables below are a starting point; use `schema://model` for the full inventory.

### Business entities and grain
- `orders` (fact): Each row represents a unique customer order placed in the system.. Grain: one row per unique order_id.
  - `order_date`: The date when the order was placed, stored as a plain date without a specific time component.
  - `order_status`: The current lifecycle status of the order.
  - `shipping_city`: The city where the order is to be shipped.

### Interpretation caveats
- The currency for 'order_total' is not specified and may be mixed; it is also unknown whether it includes tax and shipping.
- The 'order_date' column stores a plain date without any time component or timezone information.
- The nature of 'customer_id' (e.g., individual vs. business, uniqueness per customer) is not explicitly defined.
- There is no information available regarding test orders, duplicate entries, or specific date ranges with missing data.

### Questions with verified query examples
- Retrieve all columns for all orders.
- Count the total number of orders.
- List all unique order statuses.
- List all unique shipping cities.
- List all unique payment methods.
Retrieve the corresponding SQL from `schema://cookbook` before using it.

## Analytical Methodology

When analyzing business performance, follow this structured diagnostic protocol:
1. **Frame the Question**: Restate ambiguous questions with specific metrics, time horizons, and baseline periods.
2. **Decompose Metrics First**: Break composite metrics into constituent drivers (e.g. `Revenue = Volume × Unit Price × Mix`).
3. **Query via Grounded Tools**: Fetch data using the bundled MCP tools (`get_kpi`, `run_safe_query`) without guessing columns.
4. **Drill Down by Dimensions**: Compare period-over-period performance across available segments, ordering by absolute variance contribution.
5. **Synthesize with BLUF**: Lead with the Bottom Line Up Front, follow with quantified evidence, and conclude with actionable recommendations.

## Available KPIs

- `average_order_value` - **Average Order Value**: Average revenue per completed order. (unit: currency)
- `cancellation_rate` - **Order Cancellation Rate**: Percentage of orders that were cancelled. (unit: percent)
- `monthly_revenue_trend` - **Monthly Revenue Trend**: Revenue and order count from completed orders, grouped by month. (unit: currency)
- `orders_by_payment_channel` - **Orders by Payment Channel**: Order count and revenue grouped by payment channel. (unit: mixed)
- `return_rate` - **Return Rate**: Percentage of orders that were returned. (unit: percent)
- `revenue_by_customer_tier` - **Revenue by Customer Tier**: Order count and revenue grouped by the customer's loyalty tier. Joins the order table to the customer table on a verified relationship. (unit: mixed)
- `revenue_by_location` - **Revenue by Location**: Order count and revenue grouped by shipping location. (unit: mixed)
- `total_revenue` - **Total Revenue**: Total revenue from completed orders. (unit: currency)

## How to Use the MCP Analytics Tools

Before writing any SQL, read the `schema://model` resource (per-table meaning, column definitions, enum decodes) and `schema://cookbook` (verified example queries for this exact database). Use `schema://relationships` for join keys. Then follow this hierarchy:
1. **Discovery**: Call `mcp__mis-mcp-retail-ecommerce__describe_data`, `mcp__mis-mcp-retail-ecommerce__list_business_concepts`, or `mcp__mis-mcp-retail-ecommerce__list_kpis` first to discover available business entities and metrics.
2. **KPIs & Metrics**: Prefer `mcp__mis-mcp-retail-ecommerce__get_kpi` for standard verified metrics, `mcp__mis-mcp-retail-ecommerce__compare_kpi` for period comparisons, `mcp__mis-mcp-retail-ecommerce__rank_entities` for top/bottom rankings, and `mcp__mis-mcp-retail-ecommerce__breakdown_metric` for dimensional slices.
3. **Categorical Slices**: Use `mcp__mis-mcp-retail-ecommerce__get_value_set` to discover distinct category values safely without exploratory SQL.
4. **Entity Records**: Use `mcp__mis-mcp-retail-ecommerce__get_record`, `mcp__mis-mcp-retail-ecommerce__search_records`, or `mcp__mis-mcp-retail-ecommerce__get_related_records` for entity lookups.
5. **Escape Hatch**: Use `mcp__mis-mcp-retail-ecommerce__run_safe_query` ONLY as a last resort when no semantic or metric tool can answer the question.

## Guardrails & Privacy

- All data access must be read-only
